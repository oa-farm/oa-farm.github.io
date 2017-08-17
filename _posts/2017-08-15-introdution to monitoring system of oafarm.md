---
layout: post
title:  "Tản mạn về hệ thống IoT của oa-farm"
date:   2017-06-30 21:20:00
categories: "IT"
---

Oa-farm xin chia sẽ vài tản mạn về quá trình lập trình hệ thống IoT (Internet of things) nhằm thu thập các dữ liệu của, nhằm tối ưu việc quản lý nông trại.

Nói ra thì dài dòng, đại khái là lúc đầu oa-farm chia hệ thống thành 3 phần để tiến hành thực hiện:

1. Trên Raspberry pi, một mạch máy tính mini nhưng có chức năng kết nối với các cảm biến (sensor). Các đoạn python scripts được viết đơn giản để thu nhận dữ liệu như nhiệt độ, độ ẩm, ánh sáng, pH ... và cả dữ liệu hình ảnh từ camera. Tiếp đó công cụ cron trên linux được sử dụng để các đoạn script này cứ 5 phút là tự động chạy 1 lần và gửi dữ liệu về back-end thông qua giao thức HTTP (post) (riêng camera thì 30 giây gửi ảnh về 1 lần, phần camera viết cũng tương đối dài dòng, có dịp mình sẽ viết 1 bài riêng về nó)


2. Một server để lưu trữ cơ sở dữ liệu và chạy back-endservice được viết trên Django. Trên server này, dữ liệu được lưu trữ trong MongoDB. Các dữ liệu thu thập được trên các sensor giao tiếp với back-end services và database thông qua rest-full api (Application programming interface).

3. Hệ thống front-end được viết trên react. Đây là phần phụ trách trình diễn dữ liệu cho người dùng một cách trực quan.

![Giao diện web hiển thị dữ liệu từ các cảm biến](http://i.imgur.com/27UXWTT.png)

Sau 1 thời gian không ngắn, cả ba phần trên cuối cùng cũng được các thành viên của oa-farm viết xong. Nhưng khi triển khai thực tế trên nông trại thì lại có một số vấn đề nảy sinh:

1. Kinh phí để mua 1 máy tính để làm back-end server bị cắt giảm. Điều này cũng có thể khắc phục bằng sử dụng dịch vụ đám mây EC2 của Amazon, nhưng mà các tài khoản free dùng để làm chuyện khác rồi, nên phải tìm cách khác.
2. Không có mạng cáp quang.
3. Dữ liệu ảnh từ ip camera khá nhiều, sớm muộn thì ổ cứng cũng sẽ hết dung lượng.

Đúng là trong tình trạng tài chính không dư dả, bắt buộc ta phải đưa ra những giải pháp thích ứng với nó. Sau 1 thời gian suy nghĩ, quyết định bỏ qua các khía cạnh về bảo mật, oa-farm quyết định lần lượt giải quyết các vấn đề trên như sau:

1. Sử dụng dịch vụ google docs của google để lưu dữ liệu từ các cảm biến (may mà google có cung cấp api cho google drive để thực hiện chuyện này).

2. Sử dụng router phát wifi từ sim 4G. Không sử dụng mạng cáp quang đồng nghĩa với dung lượng và tốc độ mạng hạng chế, từ việc này nên cũng bắt buộc oa-farm phải viết đoạn script lấy và gửi hình ảnh lên đám mây theo cách đa luồng và không đồng bộ (do mạng chậm nên trong 30 giây đoạn script này chưa chạy xong thì đoạn script khác đã được gọi tới, do đó các đoạn script phải được thực thi không đồng bộ)

3. Cần tìm 1 dịch vụ lưu trữ ảnh miễn phí và không giới hạn dung lượng. Lúc đầu thì định dùng google photo để lưu trữ ảnh từ camera nhưng tiếc là google chưa cung cấp api để upload ảnh, nên phải chuyển qua dùng Imgur.
