---
layout: post
title: Phân tích phổ âm
date: 2017-04-01
category: Algorithm
tag: [DSP, algorithm]
external: "mathjax"
---
Bài viết này sẽ giới thiệu tổng quan về tiến trình xử lý tín hiệu số được gọi là Digital Signal Processing(DSP)

## Tổng quan
**Chuỗi thời gian (Time Series)**<br>
Với mục đích của bài viết thì ta có thể hiểu rằng chuỗi thời gian là tập của các giá trị mẫu được lấy từ hàm liên tục tăng đều theo thời gian trong một khoảng thời gian xác định. Ví dụ như bạn cứ cách mỗi phút đo nhiệt độ phòng một lần liên tục trong vòng 6 tiếng thì kết quả ta có được là một tập có 360 giá trị nhiệt độ phòng được đo và tập giá trị đó được xem như là một chuỗi giờ gian.

**Tính toán**<br>
Trong DSP chúng ta thường nhân hai chuỗi thời gian lại với nhau. Khi ta lấy chuỗi thời gian $$x(n)$$ nhân với chuỗi thời gian $$y(n)$$ ta được một chuỗi thời gian mới cọi là $$w(n)$$.

Thường thì các công việc tính toán trong DSP không có gì phức tạp hơn việc lấy số trung bình trong chuỗi kết quả của tích hai chuỗi thời gian.

**Cái gì, khi nào, tại sao**<br>
Vấn đề quan trọng trong DSP là bạn phải biết chúng ta cần nhân cái gì, khi nào thì nhân và tại sao phải nhân nó. Có một vài giải thuật DSP rất là phức tạp. Ví dụ như giải thuật Fast Fourier Transform (FFT) cực kỳ phức tạp với nhiều phép tính tổng tích (multiply-add operations).

Bây giờ chúng ta sẽ tập trung vào giải thuật Discrete Fourier Transform (DFT) ít phức tạp và dễ hiểu hơn(DFT và FFT cho ra kết quả giống nhau nhưng DFT chạy chậm hơn FFT).

**Tốc độ của các phép toán tổng tích**<br>
Một vài DSP cần thực hiện một lượng cực kỳ lớn các phép toán tổng tích. Và để thực hiện DSP trong thời gian thực thì các thiết bị để thực hiện các phép toán đó phải cực kỳ nhanh.

**Biểu đồ đường cong**<br>
Nếu bạn phát thảo chuỗi thời gian bằng đường cong trên đồ thị, chúng ta sẽ thấy rằng tổng giá trị chuỗi thời gian nằm phía trên đường nằm ngang bằng với tổng giá trị chuỗi thời gian nằm phía dưới.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9OGpFNjh6aW9IbHM "Biểu đồ đường cong")<br>
_Hình 1_

**Chu kỳ**<br>
Chu kỳ của chuỗi thời gian là một trong những cái mà tập các giá trị lặp lại theo thời gian. Bạn có thể thấy trong _Hình 2_ một tập giá trị được lặp lại khi di chuyển từ trái sang phải.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9OVV6aEp5SFJBcG8 "Chu kỳ")<br>
_Hình 2_

Nhìn lên _Hình 2_ ở trên ta thấy diện tích dưới đường cong gần như bằng 0 (giả sử lấy đường ngang làm 0, phía trên đường ngang có giá trị dương, phía dưới có giá trị âm).

<!-- ![alt text](http://www.developer.com/img/articles/2004/06/29/java1478fig05.gif "Chu kỳ")<br>
_Hình 3_

Ta so sánh _Hình 2_ với _Hình 3_, ta thấy hai hình có chu kỳ giống nhau nhưng _Hình 3_ có thiên hướng tiến về giá trị dương còn _Hình 2_ thì lấy đường ngang làm tâm. Ta cũng thấy rằng _Hình 2_ có diện tích dưới đường cong bằng không trong khi đó _Hình 3_ có diện tích là một số dương. -->

## Fourier Transform
> Bất kỳ hàm có chu kỳ nào có thể được viết lại như tổng có thêm trọng số của hàm sin và cos của các tần số khác nhau. <br> _**Jean Baptiste Fourier**_

Có một quy trình toán học được biết với cái tên chuyển đổi Fourier, nó được dùng để chuyển đổi [tuyến tính](https://vi.wikipedia.org/wiki/Tuy%E1%BA%BFn_t%C3%ADnh "tuyến tính"){:target="_blank"} thông tin qua lại giữa hai miền khác nhau. Thông tin có thể hiểu như là tập các số phức trong một hoặc cả hai miền.

Các miền ở đây có thể đại diện cho nhiều thứ khác nhau. Ví dụ như ta có một miền đại diện cho các điểm ảnh(pixel) trong một tấm ảnh và có một miền khác đại diện cho các thao tác liên quan đến bức ảnh. Còn trong DSP, các miền thường được gọi là miền thời gian (time domain) và miền tần số (frequency domain).

Bình thường khi tính toán với miền thời gian và miền tần số, các giá trị trong miền thời gian là số thực trong khi đó giá trị trong miền tần số lại là số phức.

**Miền thời gian và miền tần số**<br>
Vì mục đích nào đó mà bạn muốn biết thông tin trong miền thời gian, nhưng vì một mục đích khác bạn cũng cần thông tin trong miền tần số. Chuyển đổi Fourier cho phép bạn chuyển đổi thông tin qua lại giữa hai miền này theo ý muốn.

Ví dụ như bạn hoàn toàn có thể chuyển đổi thông tin từ miền thời gian sang miền tần số, sau đó thay đổi thông tin trong miền tần số và rồi chuyển đổi thông tin đã chỉnh sửa ngược lại sang miền thời gian.

Nếu bạn vẽ một đồ thị biểu hiện đượng điện áp ảnh hưởng đến cuộn dây loa theo thời gian trong một hệ thống âm thanh thì nó là một chuỗi thòi gian, là một thành phần trong miền thời gian.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9OHgyRl9wc3VlUjA "Equalizer")<br>

Nếu bạn quan sát ánh đèn lên xuống trên bộ lọc tần số (Equalizer) khi đang chơi nhạc có nghĩa là bạn đang quan sát cùng một thông tin trong miền tần số. Thông thường ánh đèn phía beentrais đại diện cho tần số thấp hoặc bass trong khi đó ánh đền bên phải tượng trưng cho tần số cao hay treble. Thường thì nó có một thanh trượt tương ứng với mỗi nhóm cột sáng cho phép bạn áp dụng bộ lọc để nhấn mạnh một số phần của phổ tần số và làm giảm các phần các của phổ tần số.

**Chuyển đổi thuận nghịch**<br>
Có hai dạng giống nhau của chuyển đổi Fourier, chuyển đổi thuận dùng để chuyển thông tin từ miền thời gian sang miền tần số còn chuyển đổi nghịch dùng để thông tin từ miền tần số về lại miền thời gian.

**Chuỗi thời gian mẫu (Sample Time Series)**<br>
Lý thuyết của chuyển đổi Fourier được định nghĩa bằng cách áp dụng [tích phân](https://vi.wikipedia.org/wiki/T%C3%ADch_ph%C3%A2n "tích phân"){:target="_blank"} cho hàm liên tục. Trong thực tế, trong thế giới số, chúng ta hầu như không bao giờ xử lý các hàm liên tục. Thay vào đó chúng ta sẽ tính toán trên các chuỗi số rời rạc là kết quả của của một hệ thống đo lường rời rạc.<br>
(_Chúng ta từng có ví dụ ghi chép nhiệt độ căn phòng mỗi phút một lần trong 6 tiếng sẽ tạo ra một chuỗi số rời rạc_)

>Trong nhiều trường hợp, phép toán tích hợp trong tích phân có thể xấp xỉ bằng phép toán tổng hợp sử dụng dữ liệu rời rạc trong thế giới số. Đó là một trường hợp trong chuyển đổi Fourier. Như vậy, dạng tổng kết của phép chuyển đổi Fourier cho chuỗi thời gian ròi rạc được gọi là Discrete Fourier Transform (DFT)

**FFT**<br>
DFT là hoạt động tính toán lớn. Dù với những hạn chế nhất định về số lượng giá trị trong chuỗi thời gian và số lượng tần số mà nó phân tích nhưng có lại đặc biệt có hiệu quả hơn về mặt kinh tế giải thuật chuyển đổi nhanh Fourier (Fast Fourier Transform , hoặc FFT) là một lựa chọn thay thế phù hợp.

**DFT vs FFT**
Mặc dù DFT tổng quát hơn FFT nhưng FFT lại nhanh hơn DFT. Có một điều quan trọng phải hiểu rằng hai giải thuật này đơn giản là làm công việc như nhau, cùng tính ra một kết quả(nhưng FFT có hạn chế về mặt số lượng mẫu của miền thời gian và miền tần số).

Vì giải thuật DFT dễ hiểu hơn FFT nên chúng ta sẽ tập trung giải thích thuật toán DFT.

**DFT**<br>
Sau đây là các biểu thức cần phải đánh giá để xác định nội dung phổ tần số của chuỗi thời gian tại tần số $$F$$

$$Real(F) = \sum_{n = 0}^{N - 1} x(n).cos(2\pi .F.n)$$<br>
$$Imag(F) = \sum_{n = 0}^{N - 1} x(n).sin(2\pi .F.n)$$<br>
$$ComplexAmplitude(F) = Real(F) - i.Imag(F)$$<br>
$$Power(F) = Real(F).Real(F) + Imag(F).Imag(F)$$

Trước hết bạn cần nắm một vài thứ, Đầu tiên bạn có một chuỗi thời gian $$x(n)$$, bạn có thể xác định chuỗi thời gian có bao gồm thành phần $$sin$$ hoặc $$cos$$ tại tần số $$F$$ cách các bước sau:

* Tạo một chuỗi thời gian mới $$cos(n)$$, đây là một hàm $$cos$$ với tần số $$F$$.
- ạo một chuỗi thời gian mới khác $$sin(n)$$ là một hàm $$sin$$ với tần số $$F$$.
- Nhân $$x(n)$$ với $$cos(n)$$ sau đó tính tổng của các kết quả và gọi nó là $$Real(F)$$. Đây là ước tính của biên độ(nếu có) của thành phần $$cos$$ với tần số phù hợp trong chuỗi thời gian $$x(n)$$.
- Nhân $$x(n)$$ với $$sin(n)$$ sau đó tính tổng của các kết quả và gọi nó là $$Imag(F)$$. Đây là ước tính của biên độ(nếu có) của thành phần $$sin$$ với tần số phù hợp trong chuỗi thời gian $$x(n)$$.
- Ta xem các giá trị $$Real(F)$$ và $$Imag(F)$$ là các phần thực và phần ảo của số phức.
- Ta xem tổng của bình phương phần thực và phần ảo đại diện cho lũy thừa tại tần số $$F$$ trong chuỗi thời gian.

Về cơ bản thì chỉ có thế. Với mỗi tần số mà bạn quan tâm, bạn sẽ áp dụng quá trình trên để tính ra được một số phức $$Real(F) - i.Imag(F)$$ đại diện cho thành phần của tần số trong chuỗi thời gian.

Tương tự, bạn cũng có thể tính được tổng bình phương của phần thực và phần ảo và coi đó là thước đo lũy thừa tại tần số đó trong chuỗi thời gian.<br>
(_Đây là giá trị được hiển thị bởi một trong những thanh sáng trên thiết bị lọc tần số._)

Thông thường chúng ta quan tâm nhầu tần số nên sẽ lặp lại các bước trên với mỗi tần số chúng ta quan tâm.
(_Điều này sẽ tạo ra các tập giá trị được hiển thị trên tất cả các cột sáng của bộ lọc tần số._)

Ta có một vài hệ thức lượng giác sau:

$$sin(a).sin(b)=\frac{cos(a-b)-cos(a+b)}{2}$$<br>
$$cos(a).cos(b)=\frac{cos(a-b)+cos(a+b)}{2}$$<br>
$$sin(a).cos(b)=\frac{sin(a+b)+sin(a-b)}{2}$$

Mặc dù các hệ thức trên áp dụng cho tích của $$cos$$ và $$sin$$ với góc $$a$$ và $$b$$, đây là hệ thức đơn gian để mở rộng và áp dụng cho tính toán với chuỗi thời gian gồm có hàm $$cos$$ và $$sin$$.

**Tích của hàm $$sin$$ và $$cos$$**<br>
Với từng trường hợp ở dưới đây thì hàm $$f(n)$$ là một chuỗi thời gian được tính bởi tích hai chuỗi thời gian khác là hàm $$sin$$ và hàm $$cos$$

$$\begin{align}f(n)&=sin(a.n).sin(b.n)\\&=\frac{cos((a-b).n)-cos((a+b).n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=cos(a.n).cos(b.n)\\&=\frac{cos((a-b).n)+cos((a+b).n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=sin(a.n).cos(b.n)\\&=\frac{sin((a+b).n)+sin((a-b).n)}{2}\end{align}$$

Sau đây chúng ta sẽ viết lại và đơn giản háo ba hàm bên trên trong trường hợp đặc biết là $$a=b$$

$$\begin{align}f(n)&=sin(a.n).sin(a.n)\\&=\frac{1-cos(2a.n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=cos(a.n).cos(a.n)\\&=\frac{1+cos(2a.n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=sin(a.n).cos(a.n)\\&=\frac{sin(2a.n)}{2}\end{align}$$

**Kết quả của tích hai hàm $$sin$$ tại cùng một tần số**<br>
Giờ chúng ta hãy áp dụng các hàm trên, ví dụ như ta có một chuỗi thời gian cho hàm đầu tiên trong ba hàm trên. Trường hợp này là kết quả của hao hàm $$sin$$ có cùng tần số, kết quả được hiển thị trong _Hình 4_.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9ZGdEMHFkcXQ3dG8 "Product two sin")<br>
_Hình 4_

Đường màu đỏ thể hiện hàm $$sin(x)$$ còn đường màu xanh là kết quả của phép nhân giữa $$sin(x)$$ và $$sin(x)$$.

Bây giờ nếu tính tổng giá trị của đường màu xanh với số chẵn chu kỳ thì tổng của nó sẽ khác 0 hay cũng có thể nói nó là một số dương.

Trở lại với biểu thức $$Imag(F)$$ (phần ảo được tính bởi tích chuỗi thời gian với hàm $$sin$$ và tổng của chúng). Nếu chuỗi thời gian có chứa thành phần $$sin$$ có cùng một tần số với hàm $$sin$$ thì thành phần đó sẽ đóng góp một giá trị khác không vào kết quả tổng. Vì vậy phần ảo của sự chuyển đổi tại tần số đó sẽ khác 0.

**Kết quả của tích hai hàm $$cos$$ tại cùng một tần số**<br>
Cũng như trên, bây giờ ta sẽ tính theo hàm thứ hai. Trường hợp này là kết quả của hao hàm $$cos$$ có cùng tần số, kết quả được hiển thị trong _Hình 5_.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9NmJ4Y245Sm9SdWM "Product two cos")<br>
_Hình 5_

Đường màu đỏ thể hiện hàm $$cos(x)$$ còn đường màu xanh là kết quả của phép nhân giữa $$cos(x)$$ và $$cos(x)$$.

Bây giờ nếu tính tổng giá trị của đường màu xanh với số chẵn chu kỳ thì tổng của nó sẽ khác 0 hay cũng có thể nói nó là một số dương.

Trở lại với biểu thức $$Real(F)$$ (phần thực được tính bởi tích chuỗi thời gian với hàm $$cos$$ và tổng của chúng). Nếu chuỗi thời gian có chứa thành phần $$cos$$ có cùng một tần số với hàm $$cos$$ thì thành phần đó sẽ đóng góp một giá trị khác không vào kết quả tổng. Vì vậy phần thực của sự chuyển đổi tại tần số đó sẽ khác 0.

**Kết quả của tích hàm $$sin$$ và hàm $$cos$$**<br>
Bây giờ sẽ tính tiếp hàm thứ ba là tích giữa hàm $$sin$$ và hàm $$cos$$ tại cùng một tần số. Kết quả đạt được như _Hình 6_.

![alt text](https://docs.google.com/uc?export=download&id=0B9ViryDHWtu9NkxmWTgtejhET1k "Product sin and cos")<br>
_Hình 6_

Đường màu đỏ là hàm $$cos(x)$$, đường màu xanh lá cây là hàm $$sin(x)$$ còn đường màu xanh dương là hàm tích giữa $$sin(x)$$ và $$cos(x)$$.

Nếu tính tổng giá trị của đường màu xanh dương với số chẵn chu kỳ thì nó sẽ bằng 0.

Vì vậy với $$Real(F)$$ chỉ đo thành phần $$cos$$ trong chuỗi thời gian ở một tần số cụ thể và với $$Imag(F)$$ chỉ đo thành phần $$sin$$ trong chuỗi thời gian có cùng tần số.

Biểu thức $$Real(F)$$ không cho ra kết quả khác 0 do thành phần $$sin$$ trong chuỗi thời gian có cùng tần số. Biểu thức $$Imag(F)$$ cũng không cho ra kết quả khác 0 do thành phần $$cos$$.

Do đó, ở một tần số cụ thể thì sự tồn tại của thành phần $$cos$$ trong chuỗi thời gian tạo ra kết quả thực, còn thành phần $$sin$$ tạo ra kết quả ảo.

**Thực tế** thì thành phần $$sin$$ tạo bởi chuỗi thời gian sẽ không phải là hàm $$sin$$ hay $$cos$$. Nó chỉ có hình dạng giống nhau nhưng tại một điểm xác định thì sẽ có giá trị khác nhau. Tuy nhiên nó chỉ ra rằng các hàm dạng hình $$sin$$ có thể được đại diện bằng tổng của hàm $$sin$$ và $$cos$$ có biên độ khác nhau.

Ở trên ta đang xét trên điều kiện là tần số của $$sin$$, $$cos$$ khớp với tần số của $$sin$$, $$cos$$ trong chuỗi thời gian. Vậy nếu như tần số không khớp nhau thì sao ?

Ta xem lại các biểu thức ở trên:

$$\begin{align}f(n)&=sin(a.n).sin(b.n)\\&=\frac{cos((a-b).n)-cos((a+b).n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=cos(a.n).cos(b.n)\\&=\frac{cos((a-b).n)+cos((a+b).n)}{2}\end{align}$$<br>
$$\begin{align}f(n)&=sin(a.n).cos(b.n)\\&=\frac{sin((a+b).n)+sin((a-b).n)}{2}\end{align}$$

Chúng ta thấy khi a khác b thì kết quả của tích hai $$sin$$ hoặc $$cos$$ sẽ là tổng của hai $$sin$$ hoặc $$cos$$ khác. Tổng giá trị qua số chẵn chu kỳ luôn luôn bằng 0.

**Nguồn**<br>
[Fun with Java, How and Why Spectral Analysis Works](http://www.developer.com/java/other/article.php/3374611 "Developer.com"){:target="_blank"}<br>
[Phân tích Fourier](http://www.math.hcmuns.edu.vn/~ptbao/BHXLA/5.pdf "hcmuns"){:target="_blank"}
