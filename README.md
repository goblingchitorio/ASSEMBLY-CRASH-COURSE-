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






























