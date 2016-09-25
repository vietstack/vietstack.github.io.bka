---
layout: post
title: OpenStack CEE Day (8-6-2015)
date: 2015-06-08 20:11
author: vnstack
comments: true
categories: [Uncategorized]
---
<strong>1. Tổng kết chung về OpenStack:</strong>

- Sự lớn mạnh của OpenStack là minh chứng rõ rệt cho gian đoạn phát triển vô cùng mạnh mẽ của của các phần mềm mã nguồn mở - tương lai của ngành IT trong thời gian sắp tới.

- Nếu xét theo 2 cấp độ về Experiment và Adoption thì hiện tại chỉ một số core project của OpenStack là “trưởng thành” và được ứng dụng rộng rãi bao gồm Keystone, Glance, Nova, Cinder, Swift. Ngoài ra một số project khác như Murano, Magnum (Container Management), Trove, Manila, etc. vẫn đang trong giai đoạn thử nghiệm.

- Docker vẫn là sự lựa chọn hàng đầu về Container, trong khi đó Puppet được chọn lựa nhiều nhất trong phân khúc management tool.

- Một trong những vấn đề của Cloud được rất nhiều vendor quan tâm chính là Cloud Federation và Private Cloud as a Service (PcaaS). Bên cạnh đó, các hardware vendor đã và đang thay đổi liên tục các sản phẩm phần cứng của họ nhằm mục đích phục vụ cho nhu cầu hỗ trợ các plugin đối với OpenStack (e.g Hỗ trợ về plugin của Cinder hay plugin cho Neutron nếu dùng sản phẩm phần mềm về Networking của chính hãng.). Đây chính là hệ quả của việc người dùng OpenStack không muốn phụ thuộc (lock down) vào phần cứng riêng biệt của từng hãng.

- Canonical đưa ra 2 vấn đề vô cùng quan trọng cũng như rất khó trong việc triển khai OpenStack đó chính là Automation và Hyperscaling. Canonical giới thiệu sản phẩm Bootstack, họ sẽ thiết kế OpenStack cloud dựa theo yêu cầu khách hàng, triển khai và thử nghiệm trên hệ thống của họ và bàn giao lại cho khách hàng nếu có yêu cầu.

- Tương lai của OpenStack chính là bài toán làm thế nào để OpenStack trở thành một platform như Google hay AWS. Ngoài ra, việc đảm bảo các ứng dụng (legacy + OpenStack App) chạy ổn định trên OpenStack cũng là một thách thức.

- Phương pháp triển khai OpenStack trên bare-metal hiện nay đạt được sự tin cậy nhiều nhất chính là sử dụng MaaS (Metal as a Service) và Juju của Canonical. MaaS là công cụ dùng để quản lý bare-metal + Juju là công cụ dùng để triển khai các dịch vụ của OpenStack. MaaS có khá nhiều ưu thế đối với việc quản lý bare-metal chính là nó hỗ trợ HA cho MaaS controller, ngoài ra đó là một công cụ tự động triển khai/phát hiện (auto deploy/discover) các bare-metal nodes. Điều này vô cùng thuận lợi cho quá trinh bảo trì các node vật lý, giảm thiểu tối đa có thể có downtime.

- VMware demo về Cloudify TOSCA - orchestration tool (tương tự HEAT) bằng việc sử dụng template để định nghĩa cũng như cấu hình cho các nodes trên nền tảng Vmware cloud - vCloud Air.

<strong>2. SDN</strong>

- HP trình bày về dự án SDN với việc sử dụng OpenDayLight (ODL). Use case của HP chính là việc sử dụng ODL RestAPI giao tiếp với Neutron của OpenStack thông qua ML2 plugin. Bên cạnh đó chính là việc ứng dụng OpenvSwitch cũng như OpenFlow làm giao thức giao tiếp đem lại cho HP lợi thế rõ rệt trong việc triển khai SDN trên KVM, Hyper-V hoặc những hypervisor hỗ trợ OVS.

- Sự ra đời của OPNFV - Arno đánh dấu sự thành công bước đầu cũng như nỗ lực của các telco vendor trong công cuộc phát triển một platform cho VNF (Virtual Network Function). Tuy nhiên đây chỉ là bước khởi đầu, OPNFV đang trong quá trình hoàn thiện các tính năng cũ, bổ sung tính năng mới như HA cho các node khi triển khai OPNFV, HA cho ODL, etc. cũng như việc cập nhật liên tục các phiên bản OPNFV để theo kịp sự phát triển của OpenStack.

&nbsp;

8/6/2015

Vietstack Team

&nbsp;
