---
title: "A primer on data structures used by Nova to represent block devices"
comments: true
categories: 
  - Tech
  - Blog
tags: 
  - Nova
  - OpenStack
  - Python
---
From Matthew Booth &lt;@mbooth&gt; at RedHat Virtualisation Team - Nova team

The purpose of this mail is to share what I have learned about the various data structures used by Nova for representing block devices. I compiled this for my own use, but I hope it might be useful for others, and that other might point out any errors.

As is usual when I'm reading code like this, I've created some cleanup patches to address nits or things I found confusing as I went along. I've posted review links at the end.

A note on reading this. I refer to local disks and volumes. A local disk in this context is any disk directly managed by nova compute. If nova is configured to use Rbd or NFS for instance disks these disks won't actually be local, but they are still managed locally and referred to as local disks.

There are 4 relevant data structures. 2 of these are general, 2 are specific to the libvirt driver.

&nbsp;

<strong>BlockDeviceMapping</strong>

===============

The 'top level' data structure is the block device mapping object. It is a NovaObject, persisted in the db. Current code creates a BDM object for every disk associated with an instance, whether it is a volume or not. I can't confirm (or deny) that this has always been the case, though, so there may be instances which still exist which have some BDMs missing.

The BDM object describes properties of each disk as specified by the user. It is initially created by the user and passed to compute api. Compute api transforms and consolidates all BDMs to ensure that all disks, explicit or implicit, have a BDM, then persists them. Look in nova.objects.block_device for all BDM fields, but in essence they contain information like (source_type='image', destination_type='local', image_id='&lt;image uuid'&gt;), or equivalents describing ephemeral disks, swap disks or volumes, and some associated data.

Reader note: BDM objects are typically stored in variables called 'bdm' with lists in 'bdms', although this is obviously not guaranteed (and unfortunately not always true: bdm in libvirt.block_device is usually a DriverBlockDevice object). This is a useful reading aid (except when it's proactively confounding), as there is also something else typically called 'block_device_mapping' which is not a BlockDeviceMapping object.

&nbsp;

<strong>block_device_info</strong>

=============

Drivers do not directly use BDM objects. Instead, they are transformed into a different driver-specific representation. This representation is normally called 'block_device_info', and is generated by virt.driver.get_block_device_info(). Its output is based on data in BDMs. block_device_info is a struct containing:

<blockquote>  {

'root_device_name': hypervisor's notion of the root device's name

'ephemerals': A list of all ephemeral disks

'block_device_mapping': A list of all cinder volumes

'swap': A swap disk, or None if there is no swap disk

}</blockquote>

The disks are represented in one of 2 ways, which depends on the specific driver currently in use. There's the 'new' representation, used by the libvirt and vmwareapi drivers, and the 'legacy' representation used by all other drivers. The legacy representation is a plain dict. It does not contain the same information as the new representation. I won't cover it further here as I haven't looked at it in detail.

The new representation involves subclasses of nova.block_device.DriverBlockDevice. As well as containing different fields, the new representation significantly also retains a reference to the underlying BDM object. This means that by manipulating the DriverBlockDevice object, the driver is able to persist data to the BDM object in the db.

Reader beware: common usage is to pull 'block_device_mapping' out of this dict into a variable called 'block_device_mapping'. This is not a BlockDeviceMapping object, or list of them.

Reader beware: if block_device_info was passed to the driver by compute manager, it was probably generated by _get_instance_block_device_info(). By default, this function filters out all cinder volumes from block_device_mapping which don't currently have connection_info. In other contexts this filtering will not have happened, and block_device_mapping will contain all volumes.

Reader beware: unlike BDMs, block_device_info does not represent all disks that an instance might have. Significantly, it will not contain any representation of an image-backed local disk, i.e. the root disk of a typical instance which isn't boot-from-volume. Other representations used by the libvirt driver explicitly reconstruct this missing disk. I assume other drivers must do the same.

&nbsp;

<strong>instance_disk_info</strong>

=============

The driver api defines a method get_instance_disk_info, which returns a json blob. The compute manager calls this and passes the data over rpc between calls without ever looking at it. This is driver-specific opaque data. It is also only used by the libvirt driver, despite being part of the api for all drivers. Other drivers do not return any data. The most interesting aspect of instance_disk_info is that it is generated from the libvirt XML, not from nova's state.

Reader beware: instance_disk_info is often named 'disk_info' in code, which is unfortunate as this clashes with the normal naming of the next structure. Occasionally the two are used in the same block of code.

instance_disk_info is a list of dicts for some of an instance's disks.

Reader beware: Rbd disks (including non-volume disks) and cinder volumes are not included in instance_disk_info.

The dicts are:

<blockquote>  {

'type': libvirt's notion of the disk's type

'path': libvirt's notion of the disk's path

'virt_disk_size': The disk's virtual size in bytes (the size the guest OS sees)

'backing_file': libvirt's notion of the backing file path

'disk_size': The file size of path, in bytes.

'over_committed_disk_size': As-yet-unallocated disk size, in bytes.

}</blockquote>

&nbsp;

<strong>disk_info</strong>

=======

Reader beware: as opposed to instance_disk_info, which is frequently called disk_info.

This data structure is actually described pretty well in the comment block at the top of libvirt/blockinfo.py. It is internal to the libvirt driver. It contains:

<blockquote>   {

'disk_bus': the default bus used by disks

'cdrom_bus': the default bus used by cdrom drives

'mapping': defined below

}</blockquote>

'mapping' is a dict which maps disk names to a dict describing how that disk should be passed to libvirt. This mapping contains every disk connected to the instance, both local and volumes.

First, a note on disk naming. Local disk names used by the libvirt driver are well defined. They are:

'disk': the root disk

'disk.local': the flavor-defined ephemeral disk

'disk.ephX': where X is a zero-based index for bdm-defined ephemeral disks.

'disk.swap': the swap disk

'disk.config': the config disk

These names are hardcoded, reliable, and used in lots of places.

In disk_info, volumes are keyed by device name, eg 'vda', 'vdb'. Different busses will be named differently, approximately according to legacy Linux device naming.

Additionally, disk_info will contain a mapping for 'root', which is the root disk. This will duplicate one of the other entries, either 'disk' or a volume mapping.

The information for each disk is:

<blockquote>   {

'bus': the bus for this disk

'dev': the device name for this disk as known to libvirt

'type': A type from the BlockDeviceType enum ('disk', 'cdrom', 'floppy', 'fs', or 'lun')

=== keys below are optional, and may not be present

'format': Used to format swap/ephemeral disks before passing to instance (e.g. 'swap', 'ext4')

'boot_index': the 1-based boot index of the disk.

}</blockquote>

Reader beware: BlockDeviceMapping and DriverBlockDevice store boot index zero-based. However, libvirt's boot index is 1-based, so the value stored here is 1-based.

&nbsp;

Cleanups

=======

<a href="https://review.openstack.org/329366">https://review.openstack.org/329366</a> rename libvirt has_default_ephemeral

<a href="https://review.openstack.org/329927">https://review.openstack.org/329927</a> Remove virt.block_device._NoLegacy exception

<a href="https://review.openstack.org/329928">https://review.openstack.org/329928</a> Remove unused context argument to _default_block_device_names()

<a href="https://review.openstack.org/329930">https://review.openstack.org/329930</a> Rename compute manager _check_dev_name to _add_missing_dev_names

======

Hanoi 06/2016

VietStack team
