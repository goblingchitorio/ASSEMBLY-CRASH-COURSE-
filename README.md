# Assembly Crash Course pwn.college Archives.
 - **Challenge:** Assembly Level 1 -> 30
- **Category:** Programming languages
- **Difficulty:** Easy
- **Source**: [pwn.college](https://pwn.college/computing-101/assembly-crash-course/)
  
-Note:Đây là write up thứ hai của mình trong hành trình thi đấu ```ctf``` nói chung và ```pwnable``` nói riêng. Một công cụ hay một nền tảng để luyện tập là vô cùng cần thiết, do đó mình sẽ chọn hoàn thành các level của ```pwn college```, cung cấp các bài học rất trực quan  dễ tiếp cận cho người mới bắt đầu. Hy vọng bài viết này sẽ cung cấp tư liệu tốt cho mọi người tham khảo.


![](IMG/img2.jpg)


Và trước khi bắt đầu mình có 1 vài docs và video liên quan đến Assembly các bạn có thể đọc thử tại đây:
- [LiveOverflow](https://www.youtube.com/watch?v=iyAyN3GFM7A&list=PLhixgUqwRTjxglIswKp9mpkfPNfHkzyeN&index=1)
- [Comprehensive assembly tutorial](https://github.com/mytechnotalent/Reverse-Engineering-Tutorial)
- [Architecture 1001: x86-64 Assembly](https://ost2.fyi/Arch1001)
- [x86_64 assembly book](https://open.umn.edu/opentextbooks/textbooks/733)
- ...vv

Và để thuận tiện trong quá trình giải các level thì mình sẽ dùng một ``` suorce code``` chung cho các scripts bằng ```python```, kết hợp với pwntool. Cụ thể :
```python
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    Code here
"""

output.write(pwn.asm(asm))
print(output.readallS())
```
- Dòng code đầu ``` import pwn``` có nghĩa là truyền thư viện ```pwntool``` vào file scripts.
- Dòng code thứ 2 ```pwn.context.update(arch="amd64")```có nghĩa là khai báo kiến trúc máy tính là ```64-bit(amd64)``` để pwntool biết cách biên dịch Assembly chính xác.
- Dòng code thứ 3 ```output = pwn.process("/challenge/run")``` có nghĩa là khởi động chương trình ```/challenge/run``` thành một tiến trình.
- Dòng code thứ 4 ```asm = " mình sẽ input vào các lệnh theo đề Assembly"```.
- Dòng code thứ 5 ```output.write(pwn.asm(asm))``` có nghĩa là hàm ```(pwn.asm(asm))```biên dịch đoạn mã Assembly thành mã máy dạng byte, sau đó gửi thẳng vào chương trình đang chạy.
- Dòng code thứ 6 ```print(output.readalls(())``` có nghĩa là đọc toàn bộ phản hồi từ chương trình(Kết quả kiểm tra và Flag) rồi in ra màn hình.

  
  ### Level 1 - set-register.
  Ở level này mình sẽ làm việc với thanh ghi ```registers``` bạn sẽ dược sửa đổi hoặc đọc các thanh ghi. Vui lòng hãy thiết lập các mục sau: ```rdi = 0x1337```.

  #### Lời giải
  Áp dụng soucre trên mình chỉ cần điền nội dung mà mình muốn thực hiện ở ```dòng code thứ 4```. Dựa vào đề bài mình có được thanh ghi ```rdi``` là một dạng truyền tham số ban đầu vào khi gọi hàm hoặc hệ thống. hương đi của mình là cần truyền dữ liệu đầu vào lên thanh ghi ```rdi```, dữ liệu đầu vào ở level nàylà ```0x1337```. Trước tiên mình cần tạo một file bash và cấp quyền cho nó. Tiếp theo mình dùng lệnh ```nano``` để ghi đề source code trên vào file bash vừa tạo. Cụ thể:
```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    mov rdi , 0x1337
"""

output.write(pwn.asm(asm))
print(output.readallS())

```
---
## Flag 
pwn.college{wEaueXOqiFTYemitXmjoPn2tIje.dRTOxwCM2EDOzIzW}.

---

### Level 2 Set-mutiple-registers
Ở level này mình sẽ làm việc trên thanh ghi. Mình sẽ sửa đổi hoặc đọc các thanh ghi. Ở level này, mình sẽ làm việc với nhiều thanh ghi. Vui lòng thiết lập các mục sau:
- rax = 0x1337
- r12 = 0xCAFED00D1337BEEF
- rsp = 0x31337

#### lời giải 
Bài này mình lệnh ```mov```(move) để gán các giá trị cho thanh ghi ```rax,r12,rsp```. Cụ thể:

```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    mov rax , 0x1337
    mov r12 ,  0xCAFED00D1337BEEF
    mov rsp , 0x31337
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

---
#### Flag
pwn.college{0Uhpj52EByMI2tP0lo_5afglR28.QXwEDOzwCM2EDOzIzW}

--- 

### Level 3 Add to register
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một số thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Có nhiều lệnh trong x86 cho phép bạn thực hiện tất cả các phép toán học thông thường trên thanh ghi và bộ nhớ.

Để viết tắt, khi chúng ta nói , thực ra nó có nghĩa là .```A += BA = A + B```

Dưới đây là một số hướng dẫn hữu ích:
```
add reg1, reg2 <=> reg1 += reg2
sub reg1, reg2 <=> reg1 -= reg2
imul reg1, reg2 <=> reg1 *= reg2
```
div phức tạp hơn, và chúng ta sẽ bàn về nó sau. Lưu ý: tất cả đều có thể được thay thế bằng hằng số hoặc vị trí bộ nhớ.regX

Thực hiện các bước sau:

-Thêm vào 0x331337 rdi.

#### Lời giải
dùng lệnh ```add``` để thêm dữ liệu ```0x3311337``` vào thanh ghi ```rdi```. Cụ thể:
- add rdi , 0x331337
```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    add rdi , 0x331337
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

#### Flag
pwn.colege{A-GNB9cxnz8fDQJPozQXH99P51R.dVTOxwCM2EDOzIzW}

---
### Level 4 linears-equation-register 
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Bây giờ chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Dựa trên kiến thức mới của bạn, vui lòng tính toán các điều sau:
```
f(x) = mx + b, trong đó:
m = rdi
x = rsi
b = rdx
Đặt kết quả vào .rax
```

Lưu ý: Có một sự khác biệt quan trọng giữa (nhân không dấu) và (nhân có dấu) về việc sử dụng các thanh ghi nào. Hãy xem tài liệu hướng dẫn này để thấy sự khác biệt.```mulimul```

Trong trường hợp này, bạn sẽ muốn sử dụng .```imul```

 #### Lời giải
 Dùng lệnh ```mov,imul và add``` để thực hiện việc tính toán các giá trị của các thanh ghi và gán lại cho thanh ```rax```. Cụ thể:
 
- mov rax , rdi
- imul rax , rsi
- add rax , rdx
```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    mov rax ,rdi
    imul rax , rsi
    add rax , rdx
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

#### Flag
pwn.college{sM83wUS-7lMp7L-Y6gdgGu_vEnP.dZTOxwCM2EDOzIzW}

---

### Level 5 integer-division
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước các thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu, thường là .rax

Phép chia trong x86 đặc biệt hơn so với toán học thông thường. Toán học ở đây gọi là toán nguyên, nghĩa là mỗi giá trị đều là một số nguyên.

Ví dụ: trong toán học số nguyên.10 / 3 = 3

Tại sao?

Bởi vì được làm tròn xuống thành số nguyên.3.33

Các hướng dẫn liên quan cho cấp độ này bao gồm:

mov rax, reg1
div reg2

Lưu ý: là một lệnh đặc biệt có thể chia một số chia 128-bit cho một ước 64-bit trong khi lưu cả thương số và phần dư, chỉ sử dụng một thanh ghi làm toán hạng.div

Lệnh phức tạp này hoạt động và hoạt động như thế nào trên một số phận 128-bit (lớn gấp đôi một thanh ghi)?div

Đối với lệnh , các thao tác sau xảy ra:div reg

rax = rdx:rax / reg
rdx = remainder
rdx:rax có nghĩa là đó sẽ là 64-bit trên của cổ tức 128-bit và sẽ là 64-bit dưới của cổ tức 128-bit.rdxrax

Bạn phải cẩn thận với những gì có trong và trước khi gọi.rdxraxdiv

Vui lòng tính toán các giá trị sau:
```
speed = distance / time, trong đó:
distance = rdi
time = rsi
speed = rax
```
Lưu ý rằng khoảng cách sẽ tối đa là giá trị 64 bit, nên khi chia nên bằng 0.rdx

#### Lời giải
Dùng lệnh ```mov và div``` để tính toán các giá trị của ```speed```. Cụ thể:
- mov rax , rdi
- div rsi

```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
    mov rax , rdi
    div rsi
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

#### Flag
pwn.college{QrxP5ZeXKpV7q6_59PA2s7_xv2j.ddTOxwCM2EDOzIzW}\

---
### Level 6 Modulo-operation
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một phép toán công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Modulo trong assembly cũng là một ý tưởng thú vị!

x86 cho phép bạn lấy phần còn lại sau một thao tác.```div```

Ví dụ: kết quả là phần dư của .10 / 31

Phần còn lại giống như modulo, còn gọi là toán tử "mod".

Trong hầu hết các ngôn ngữ lập trình, chúng ta gọi mod bằng ký hiệu ```.%```

Vui lòng tính toán các giá trị sau: ```rdi % rsi```

Đặt giá trị vào .rax

#### Lời giải
Dùng lệnh ``` mov và div``` để tính táon các giá trị ```rdi%rsi```. Cụ thể:
- mov rax , rdi
- div rsi
- mov rax , rdx

```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
  mov rax , rdi
  div rsi
mov rax , rdx
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

#### Flag
pwn.college{M5CzMm_taqnmg02YpNUp_jvDpsp.dhTOxwCM2EDOzIzW}

---
#### Level 7 set -upper-bytes
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết thanh ghi nào được thiết lập trước và bạn nên đặt kết quả ở đâu, thường là trong .rax

Một khái niệm thú vị khác trong x86 là khả năng truy cập độc lập các byte thanh ghi thấp hơn.

Mỗi thanh ghi trong x86_64 có kích thước 64 bit, và ở các cấp trước, chúng ta đã truy cập toàn bộ thanh ghi bằng cách sử dụng , , hoặc .raxrdirsi

Chúng ta cũng có thể truy cập các byte thấp hơn của mỗi thanh ghi bằng các tên thanh ghi khác nhau.

Ví dụ, 32 bit thấp hơn của có thể truy cập bằng , 16 bit dưới bằng , và 8 bit dưới có thể truy cập bằng .raxeaxaxal
```
MSB                                    LSB
+----------------------------------------+
|                   rax                  |
+--------------------+-------------------+
                     |        eax        |
                     +---------+---------+
                               |   ax    |
                               +----+----+
                               | ah | al |
                               +----+----+
```
Truy cập byte thanh ghi thấp hơn áp dụng cho hầu hết các thanh ghi.

Chỉ sử dụng một lệnh di chuyển, vui lòng đặt 8 bit cao của thanh ghi thành .```ax 0x42```

#### Lời giải
Đề yêu cầu mình dùng lệnh ```mov``` để đặt vào 8 bit cao với dữ liệu ```0x42``` của thanh ```ax```. Cụ thể:
- mov ah 0x42

```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
   mov ah 0x42
"""

output.write(pwn.asm(asm))
print(output.readallS())

```

#### Flag
pwn.college{ky8J62NZaP26Fs1LOuPEm-OsKAr.QXxEDOzwCM2EDOzIzW}

---
### Level 8 efficient-modulo
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Hóa ra việc sử dụng toán tử để tính toán phép toán modulo là chậm!div

Chúng ta có thể dùng mẹo toán học để tối ưu hóa toán tử modulo (). Trình biên dịch dùng mẹo này rất nhiều.%

Nếu ta có , và là lũy thừa của 2, ví dụ , kết quả sẽ là các bit nhỏ hơn của .x % yy2^nnx

Do đó, chúng ta có thể sử dụng truy cập byte thanh ghi thấp hơn để triển khai hiệu quả modulo!

Chỉ sử dụng các lệnh sau: ```mov```

Vui lòng tính toán các giá trị sau:
```
rax = rdi % 256
rbx = rsi % 65536
```

#### Lời giải
Chỉ dùng lệnh ```mov``` để thực hiện việc chia lấy dư của các giá trị trên. Mình phân tích ```256=2^8``` và ```65536=2^16```. Vì thế mình sẽ lấy thanh ghi có kích thước dữ liệu nhỏ nhất của ```rax``` và thanh ghi có kích thước dữ liệu (16-bit) của ```rbx```. Cụ thể:
- mov al , dil
- mov bx , si

```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
mov al , dil
mov bx , si
"""

output.write(pwn.asm(asm))
print(output.readallS())
```
#### Flag 
pwn.college{oBEUyV8iUsoxDCP5a3bHtOm6Uro.dlTOxwCM2EDOzIzW}

---

### Level 9 Byte-extraction
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Ở cấp độ này, bạn sẽ làm việc với logic bit và các phép toán. Điều này đòi hỏi phải sử dụng nhiều việc tương tác trực tiếp với các bit được lưu trữ trong thanh ghi hoặc vị trí bộ nhớ. Bạn cũng có thể cần sử dụng các lệnh logic trong x86: ```and, or,  not, xor```.

Việc dịch chuyển các chi tiết trong quá trình lắp ráp cũng là một ý tưởng thú vị!

x86 cho phép bạn ```'dịch chuyển'```  các bit trong thanh giấy.

Lấy ví dụ, 8 bit nhỏ nhất (hoặc 8 bit có ý nghĩa thấp nhất) của .```al rax```

Giá trị trong (tính bằng bit) là:```al```

```rax = 10001010```
Nếu ta dịch sang trái một lần theo lệnh sau: ```shl ```

```shl al, 1```
Giá trị mới là:

```al = 00010100```
Mọi thứ dịch chuyển sang trái, và bit cao nhất (hoặc quan trọng nhất) rơi ra trong khi một số 0 mới được thêm vào bên phải.

Bạn có thể dùng nó để làm những điều đặc biệt với những phần bạn quan tâm.

- Dịch chuyển có tác dụng phụ là nhân nhanh (nhân 2) hoặc chia (2), và cũng có thể dùng để tính modulo.

- Dưới đây là những hướng dẫn quan trọng:

```
shl reg1, reg2 <=> Dịch sang trái theo lượng trong reg1reg2
shr reg1, reg2 <=> Dịch sang phải theo lượng trong reg1reg2
```
- Lưu ý: 'reg2' có thể được thay thế bằng hằng số hoặc vị trí bộ nhớ.

Khi nói về significant bit hoặc least significant byte, ý nghĩa là:

Least significant bit/byte mang giá trị nhỏ nhất (vị trí "thấp nhất"). 

- Khi bạn thay đổi bit "thấp nhất" hoặc "bên phải nhất", giá trị chỉ thay đổi 1 đơn vị.
- Most significant bit/byte mang giá trị lớn nhất (vị trí "cao nhất").

Đối với thử thách này, chỉ sử dụng các hướng dẫn sau:

```mov, , shrshl```

Vui lòng thực hiện các bước sau: Đặt thành byte ít ý nghĩa thứ 5 của .```(rax -> rdi)```

Ví dụ:

```
rdi = | B7 | B6 | B5 | B4 | B3 | B2 | B1 | B0 |
Set rax to the value of B4
```

#### Lời giải

- Đối với bài này mình sẽ sử dụng lệnh ```mov, shl, shr``` để giải. Đề yêu cầu mình đặt byte có giá trị nhỏ nhất ở vi trí thứ 5 của thanh ghi ```rdi```.
- Hướng tư duy: Nhìn vào giản đồ mà đề cung cấp mình nhận thấy ngay vị trí có giá tri nhỏ nhất là ở ```B0``` thế nên việc của mình cần là làm sao đặt giá trị ở vị trí thứ 5 tức ```B4``` vào vị trí ```B0```. Cụ thể:

- mov rax , rdi
- shr rax , 32      ## đẩy các giá trị của thanh ghi sang phải 32 bit
- shl rax , 56
- shr rax , 56

- 2 lệnh ở cuổi có ý nghĩa là: Sau khi đẩy các giá trị của thanh ghi ```rdi``` sang phải 32 bit thì mình đã vô tình kéo theo các ```bit rác``` tràn vào các vị trí ```B7 B6 B5```. Do đó mình cần đẩy các giá trị đó ra khỏi thanh ghi.



```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
mov rax , rdi
shr rax , 32
shl rax , 56
shr rax , 56
"""

output.write(pwn.asm(asm))
print(output.readallS())
```
--- 
#### Flag
pwn.college{gY5oYfT4lyuebseZvfzSqYGuZL4.dBDMywCM2EDOzIzW}

### Level 10 Bitwise-and 
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, đó là .rax

Ở cấp độ này, bạn sẽ làm việc với logic bit và các phép toán. Điều này đòi hỏi phải sử dụng nhiều việc tương tác trực tiếp với các bit được lưu trữ trong thanh ghi hoặc vị trí bộ nhớ. Bạn cũng có thể cần sử dụng các lệnh logic trong x86: ```and, or, not, xor```

- ```Logic - bitwise``` trong hợp ngữ cũng là một khái niệm thú vị! 

- x86 cho phép bạn thực hiện các phép toán logic từng bit trên các thanh ghi.

Trong ví dụ này, giả sử các thanh ghi chỉ lưu trữ 8 bit.

Các giá trị trong và là:```rax rbx```

```
rax = 10101010
rbx = 00110011
```

Nếu chúng ta thực hiện một bit AND của và sử dụng lệnh này, kết quả sẽ được tính bằng cách AND từng cặp bit một, do đó nó được gọi là logic bitwise giữa 2 thanh ghi rax và  rbx có khối lệnh là:

```and rax, rbx```

Vậy từ trái sang phải:

```
1 VÀ 0 = 0
0 VÀ 0 = 0
1 VÀ 1 = 1
0 VÀ 1 = 0
...
Cuối cùng, chúng tôi kết hợp các kết quả lại để thu được:

```rax = 00100010```
Dưới đây là một số bảng chân lý để tham khảo:

- And

```
A | B | X
---+---+---
0 | 0 | 0
0 | 1 | 0
1 | 0 | 0
1 | 1 | 1
- OR

```
A | B | X
---+---+---
0 | 0 | 0
0 | 1 | 1
1 | 0 | 1
1 | 1 | 1
```

- XOR
```
A | B | X
---+---+---
0 | 0 | 0
0 | 1 | 1
1 | 0 | 1
1 | 1 | 0
```
Không sử dụng các hướng dẫn sau:  mov,  xchg

Vui lòng thực hiện các bước sau:

Đặt thành giá trị của rax(rdi AND rsi)

LƯU Ý: sẽ đặt tất cả các bit thành Nếu không thì màn này sẽ khó hơn!rax1

#### lời giải 
- ở bài này đề không cho sử dụng lệnh ```mov``` nên mình sẽ dùng một lệnh logic đó là ``` and ``` để đặt giá trị của thanh rax qua phép ```and``` của 2 thanh ghi ``rdi`` và ```rsi```.
Cụ thể:


- and rax , rdi
- and rax, rsi



```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
and rax , rdi
and rax, rsi
"""

output.write(pwn.asm(asm))
print(output.readallS())
```
---
#### Flag 
pwn.college{8qbV99i3ISTu71w_ctVZaZVYQ6E.dFDMywCM2EDOzIzW}

### Level 11 Check-even
Ở cấp độ này, bạn sẽ làm việc với các thanh ghi. Bạn sẽ được yêu cầu chỉnh sửa hoặc đọc từ các thanh ghi.

Chúng ta sẽ thiết lập một số giá trị trong bộ nhớ động trước mỗi lần chạy. Ở mỗi lần chạy, các giá trị sẽ thay đổi. Điều này có nghĩa là bạn sẽ cần thực hiện một loại thao tác công thức với các thanh ghi. Chúng tôi sẽ cho bạn biết trước những thanh ghi nào được thiết lập và bạn nên đặt kết quả ở đâu. Trong hầu hết các trường hợp, nó là .```rax ```

Ở cấp độ này, bạn sẽ làm việc với logic bit và các phép toán. Điều này đòi hỏi phải sử dụng nhiều việc tương tác trực tiếp với các bit được lưu trữ trong thanh ghi hoặc vị trí bộ nhớ. Bạn cũng có thể cần sử dụng các lệnh logic trong x86:  ```and or not xor```

Chỉ sử dụng các hướng dẫn sau: ```and, or, xor```

Triển khai logic sau:
```
if x is even then
  y = 1
else
  y = 0
```

Trong đó:

x = rdi
y = rax

#### Lời giải
- Để giải được level này mình cần phải làm quen với các lệnh logic trong x86:
 **And**(phép và) -> ```mặt nạ lọc và xóa bit```:
+ Nguyên lí trả về 1 nếu cả 2 bit đều là 1. Bất kì bit nào khi thực hiện phép và với số 0 thì giá trị trả về sẽ là 0.
+ Chức năng chính là trích xuất một bit/bytes cụ thể và dọn sạch các bit rác xung quanh

**Or**(phép hoặc)-> ```bật hay thiết lập bit ```
+ Nguyên lý: Trả về 1 nếu có ít nhất một bit là 1. Bất kỳ bit nào OR với 1 đều chắc chắn trở thành 1, OR với 0 thì giữ nguyên giá trị cũ.

+ Chức năng chính: Dùng để chủ động bật một hoặc nhiều bit cụ thể lên 1 mà không làm hỏng dữ liệu của các bit khác.

**XOR** (Phép HOẶC LOẠI TRỪ) - Đảo bit và Xóa trắng:

- Nguyên lý: Trả về 1 nếu hai bit có giá trị khác biệt nhau, và trả về 0 nếu chúng giống nhau.

- Chức năng 1 (Đảo bit - Toggle): Bất kỳ bit nào XOR với 1 cũng sẽ bị đảo ngược (0 thành 1, 1 thành 0).

- Chức năng 2 (Xóa trắng thanh ghi cực nhanh): Trong Assembly, xor rax, rax là tiêu chuẩn vàng để gán thanh ghi về giá trị 0. Vì mọi bit so sánh với chính nó đều trả về 0, lệnh này dọn sạch thanh ghi nhanh và tốn ít dung lượng mã máy hơn lệnh ```mov rax, 0```.


**Not**(Phép PHỦ ĐỊNH) - Đảo ngược toàn cục:

- Nguyên lý: Là lệnh chỉ có một toán hạng(Phép toán một ngôi). Nó lật ngược trạng thái của toàn bộ 64 bit trong thanh ghi cùng một lúc (1 biến thành 0, và 0 biến thành 1).

- Để giải quyết bài tập trên mình sẽ sử dụng lệnh ```and or và xor```. Để dễ hình dung về bài tập này thì có một tips có thể áp dụng đó là: để kiểm tra tính chẳn lẻ của một thanh ghi thì chỉ phụ thuộc vào vị trí có giá trị bit thấp nhất là vị trí số 0, do đó trước khi thực hiện việc kiểm tra mình nên đặt lại các giá trị cho thanh ghi rdi cso mặt nạ ```0x1``` có nghĩa là cấc vị trí bit từ 1 dến 63 đều bằng 0 chỉ giữ lại giá trị ở vị trí số 0. Tiếp theo là mình sẽ xóa trắng thanh ghi rax để việc so sánh được thực hiện một cách chính xác nhất, tiếp theo mình sẽ thực hiện bật bit lên một của thanh ghi ```rax``` phù hợp với nguyên lí của  phép hoặc loại trừ ```xor```. Cụ thể:

- and rdi , 0x1
- and rax , 0
- or rax , 1
- xor rax , rdi



```
import pwn

pwn.context.update(arch="amd64")
output = pwn.process("/challenge/run")

asm = """
and rdi , 0x1
and rax , 0
or rax , 1
xor rax , rdi
"""

output.write(pwn.asm(asm))
print(output.readallS())
```
---
#### Flag
pwn.college{khUzUPsOBWHOYTiEoUicuDncROq.dJDMywCM2EDOzIzW}









