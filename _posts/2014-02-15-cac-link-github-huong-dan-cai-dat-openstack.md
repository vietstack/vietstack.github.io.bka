---
title: "Các link - github hướng dẫn cài đặt OpenStack"
comments: true
categories: 
  - Tech
  - Blog
tags:
  - OpenStack
---
Khi mới tìm hiểu vấn đề gì đó, chúng ta thường cố gắng tìm các cài đặt và dùng thử để xem tính khả thi và "mắt thấy tay sờ", đây cũng là tâm lý chung, có thể nó không phải là bước đúng đắn khi tìm hiểu vấn đề nhưng tôi cũng đã từng làm như vậy - về sau tôi sẽ sửa lối tư duy này khi tìm hiểu vấn đề gì đó. Vì vậy bài viết này sẽ tập hợp các hướng dẫn giúp cài đặt OpenStack mà tôi tập hợp được và đã trải qua. Hi vọng rút ngắn được thời gian tìm kiếm và  mong rằng sau khi làm theo tôi và các bạn sẽ có những cách cài và kỹ thuật cài chuẩn chỉ. <strong>NHƯNG TÔI KHUYÊN BẠN HÃY TÌM HIỂU VỀ OPENSTACK TRƯỚC KHI CÀI</strong><!--more-->

<strong>Về cơ bản việc cài đặt OpenStack có vài cách mà tôi đúc rút ra như sau: (nếu thiếu các bạn bổ sung)</strong>
<ul>
	<li>Cài bằng tay (gõ từng dòng lệnh) - theo tài liệu hướng dẫn từ trang chủ.</li>
	<li>Cài bằng các kịch bản do cá nhân tự phát triển - các cá nhân tự viết ra kịch bản cài đặt, các kịch bản shell, python, perl ... Các cá nhân này đã đọc tài liệu và tổng hợp lại các bước vào trong các script. Tùy theo scirpt mà các bước có thể khác nhau</li>
	<li>Cài bằng các công cụ do bên thứ 3 phát triển (devstack, rdo, puppet ...) - có thể hiểu là cài tự động.</li>
</ul>
Và trong bài viết này tập hợp các blog, các link trên github ... hướng dẫn giúp cài đặt OpenStack theo dạng 2 - dạng kịch bản  nếu bạn đang tìm hiểu và mong muốn cài được OpenStack để xem hình thù như thế nào thì có thể tham khảo một trong các link này.

Github là một ứng dụng để chứa các mã nguồn, tài liệu, kịch bản dùng trong nhiều mục đích khác nhau rất tiện lợi (đặc biệt với những người sử dụng linux). Do đó các cá nhân thường có những hướng dẫn cài đặt OpenStack theo dạng step by step, các hướng dẫn cài đặt OpenStack này thường được thực hiện bằng các kịch bản (file shell, python, perl ....) và được chứa trên github.

Để cài được OpenStack thì các bạn chỉ cần biết sử dụng linux, đọc các hướng dẫn đi kèm và thực hiện theo hướng dẫn mà các tác giả gửi lên. Đa số các hướng dẫn này được cập nhật và sửa lỗi liên tục, do vậy nếu làm theo hướng dẫn nào đó bạn cần phải đọc kỹ và chắc chắn đã làm đúng (đúng mô hình, đúng bước, đúng nền tảng sử dụng như Ubuntu, Centos, Debian ...

<strong>Các chú ý trước khi cài:</strong>
<ul>
	<li>Các link này được sưu tập và tham khảo trên internet, nên cần học tập và đúc rút ra kinh nghiệm cài.</li>
	<li>Mỗi kịch bản cài có thể thiếu các thành phần (project con trong phiên bản OpenStack)</li>
	<li>Các hướng dẫn có thể là tham khảo của nhau (tức là tác giả này tham khảo tác giả khác và sửa đổi cho phù hợp)</li>
	<li><span style="line-height:1.5em;">Bạn cần biết sử dụng github (tải các kịch bản cài đặt, các script). </span></li>
	<li>Cần đọc các script mà các tác giả viết để biết xem có các thành phần nào được cài đặt và các thông số mà họ thiết lập.</li>
	<li>Bạn cần sử dụng linux với các lệnh cơ bản để có thể tải về, nếu chưa hiểu lệnh khi thực hiện scrpit thì tra luôn để học và hiểu.</li>
	<li>Cần đọc và nắm rõ mô hình cài đặt, số các node -máy vật lý, số card mạng, cách thiết lập IP.</li>
	<li>Nếu bạn đã thực hiện thành công theo hướn dẫn nào mà chưa có thì có thể gửi để tôi bổ sung.</li>
	<li>Nếu bạn phát hiện lỗi, hay trao đổi và đóng góp với tác giả.</li>
	<li>Tôi không đủ thời gian và thiết bị để kiểm tra từng script nên không có khả năng giải quyết các câu hỏi, hãy liên hệ với tác giả để trao đổi.</li>
	<li>Hãy học và phát triển các script để cài.</li>
</ul>
<strong>Các Link  trên github hướng dẫn cài đặt OpenStack</strong>
<ol>
	<li>https://github.com/fornyx/OpenStack-Havana-Install-Guide/blob/master/OpenStack-Havana-Install-Guide.rst (một node)</li>
	<li>https://github.com/Ch00k/openstack-install-aio/blob/master/openstack-all-in-one.rst (một node - All in one)</li>
	<li>https://github.com/kjtanaka/deploy_havana</li>
	<li>https://github.com/romilgupta/openstack-scripts (viết bằng python)</li>
	<li>https://github.com/DylanYu/Openstack-Havana-Install-Guide/blob/master/OpenStack-Havana-Install-Guide.rst</li>
	<li>https://gist.github.com/tmartinx/7019826</li>
	<li>https://github.com/jedipunkz/openstack_havana_deploy</li>
	<li>https://github.com/HolySparky/OpenStack_Installer</li>
	<li>https://github.com/mseknibilel/OpenStack-Grizzly-Install-Guide (Phiên bản được tham khảo khá nhiều, từ hướng dẫn này, các tác giả tham khảo và viết ra các hướng dẫn khác, trong này có đủ các mô hình và các thành phần).</li>
	<li>https://github.com/dmitry-teselkin/havana-from-packages</li>
	<li>https://github.com/life1347/havana-auto-deploy</li>
	<li>https://github.com/alfredcs/OpenStack-Havana</li>
</ol>
Các hướng dẫn khác:
<ol>
	<li>http://vlabs.cfapps.io/openstack/installation/overview.html (Link này mới cập nhật, khá hay-10/03/2014 )</li>
	<li>http://virtual2privatecloud.com/install-havana-on-ubuntu/</li>
	<li>http://www.cloudcraft.cn/openstack-havana-install-in-ubuntu12-04-3/</li>
	<li>http://openstack.redhat.com/Quickstart</li>
	<li><a href="http://devstack.org/guides/single-machine.html">http://devstack.org/guides/single-machine.html</a> hoặc bài viết trên vietsi đã chia sẻ</li>
	<li>http://www.andrewklau.com/getting-started-with-multi-node-openstack-rdo-havana-gluster-backend-neutron/
link2 (3 node trên RDO)</li>
	<li>http://blog.csdn.net/ifzing/article/details/9398029 (3 node với Ubuntu, tác giả người tàu)</li>
	<li>http://www.server-world.info/en/note?os=CentOS_6&amp;p=openstack_havana&amp;f=1 ( Link hướng dẫn của người Nhật)</li>
	<li>http://www.stackinsider.org/wiki/  Trang này có đơn vị quản lý là Stackinsider, họ thực hiện các mục tiêu Deploy as a Service. Họ có các bài viết hướng dẫn cài đặt OpenStack trên devstack, rdo, fule ...</li>
	<li>http://www.discoposse.com/index.php/2014/01/26/openstack-havana-all-in-one-lab-on-vmware-workstation/ (trang này rất phù hợp vơi những người chưa biết gì, kể cả vmware workstation).</li>
</ol>
<strong><em>(Cập nhật ngày 21/02/2014 với link số 6 và số 7 trong "Các hướng dẫn khác)
</em></strong><strong><em>(Cập nhật ngày 23/02/2014 với link số 8 và số 9"Các hướng dẫn khác)
<strong><em>(Cập nhật ngày 11/03/2014 với link số 1và số 10"Các hướng dẫn khác. Link số 10, 11, 12 cho phần link trên github)</em></strong>
</em></strong>
Mời các bạn bổ sung tiếp.
