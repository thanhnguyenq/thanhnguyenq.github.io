---
layout: post
title: Phân tích quang phổ với java
date: 2017-04-01
category: Java
tag: [java, tutorial]
external:
---
# Phân tích quang phổ với java
Bài viết này sẽ giới thiệu tổng quan về tiến trình xử lý tín hiệu số được gọi là Digital Signal Processing(DSP)

## Tổng quan
**Chuỗi thời gian (Time Series)**<br>
Với mục đích của bài viết thì ta có thể hiểu rằng chuỗi thời gian là tập của các giá trị mẫu được lấy từ hàm liên tục tăng đều theo thời gian trong một khoảng thời gian xác định. Ví dụ như bạn cứ cách mỗi phút đo nhiệt độ phòng một lần liên tục trong vòng 6 tiếng thì kết quả ta có được là một tập có 360 giá trị nhiệt độ phòng được đo và tập giá trị đó được xem như là một chuỗi giờ gian.

**Tính toán**<br>
Trong DSP chúng ta thường nhân hai chuỗi thời gian lại với nhau. Khi ta lấy chuỗi thời gian x(n) nhân với chuỗi thời gian y(n) ta được một chuỗi thời gian mới cọi là w(n).

Thường thì các công việc tính toán trong DSP không có gì phức tạp hơn việc lấy số trung bình trong chuỗi kết quả của tích hai chuỗi thời gian.

**Cái gì, khi nào, tại sao**<br>
Vấn đề quan trọng trong DSP là bạn phải biết chúng ta cần nhân cái gì, khi nào thì nhân và tại sao phải nhân nó. Có một vài giải thuật DSP rất là phức tạp. Ví dụ như giải thuật Fast Fourier Transform (FFT) cực kỳ phức tạp với nhiều phép tính tổng tích (multiply-add operations).

Bây giờ chúng ta sẽ tập trung vào giải thuật Discrete Fourier Transform (DFT) ít phức tạp và dễ hiểu hơn(DFT và FFT cho ra kết quả giống nhau nhưng DFT chạy chậm hơn FFT).

**Tốc độ của các phép toán tổng tích**<br>
Một vài DSP cần thực hiện một lượng cực kỳ lớn các phép toán tổng tích. Và để thực hiện DSP trong thời gian thực thì các thiết bị để thực hiện các phép toán đó phải cực kỳ nhanh.

**Biểu đồ đường cong**<br>
Nếu bạn phát thảo chuỗi thời gian bằng đường cong trên đồ thị, chúng ta sẽ thấy rằng tổng giá trị chuỗi thời gian nằm phía trên đường nằm ngang bằng với tổng giá trị chuỗi thời gian nằm phía dưới.

![alt text](http://www.developer.com/img/articles/2004/06/29/java1478fig03.gif "Biểu đồ đường cong")<br>
_Hình 1_

**Chu kỳ**<br>
Chu kỳ của chuỗi thời gian là một trong những cái mà tập các giá trị lặp lại theo thời gian. Bạn có thể thấy trong _Hình 2_ một tập giá trị được lặp lại khi di chuyển từ trái sang phải.

![alt text](http://www.developer.com/img/articles/2004/06/29/java1478fig04.gif "Chu kỳ")<br>
_Hình 2_

![alt text](http://www.developer.com/img/articles/2004/06/29/java1478fig05.gif "Chu kỳ")<br>
_Hình 3_

**Nguồn**
http://www.developer.com/java/other/article.php/3374611
