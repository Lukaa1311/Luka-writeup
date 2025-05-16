## pwn.college Writeup 
- Pwntools : https://docs.pwntools.com/en/stable/
  
+ Code snippet (From https://dvvyn.medium.com/pwn-college-assembly-crash-course-cb0eb4aba719)
  
  from pwn import *
  
  context.update(arch='amd64')
  
  code = asm("""Put the assembly code here""")
  
  p = process('/challenge/run')
  
  p.write(code)
  
  print(p.readall())

  
- Assembly Crash Course:
   1. Set register : In this level, you will be working with registers. You will be asked to modify or read from registers.
      In this level, you will work with registers!

      Please set the following: rdi = 0x1337

   Với đề bài này, mov giá trị 0x1337 vào thanh ghi rdi : mov rdi, 0x1337
  
   3. Set multiple register : In this level, you will work with multiple registers. Please set the following:
  
      rax = 0x1337
      
      r12 = 0xCAFED00D1337BEEF
      
      rsp = 0x31337
      
   Solution: mov rax, 0x1337
             mov r12, 0xCAFED00D1337BEEF
             mov rsp, 0x31337
  
   Lưu ý: khi nộp bài để lấy flag, để í khoảng trắng trong code snippet nếu không sẽ báo lỗi unexpected indent.
  
   5. add-to-register:
     Do the following:

   Add 0x331337 to rdi
  
   Solution : add rdi, 0x331337
  
   6. linear-equation-registers:

   Using your new knowledge, please compute the following:
  
   f(x) = mx + b, where:
  
   m = rdi
  
   x = rsi
  
   b = rdx
  
   Place the result into rax.
  
   Solutions :
               imul rdi, rsi
  
               add rdi, rdx
  
               mov rax, rdi 
   Note : Phân biệt sự khác nhau giữa mul(unsigned multiply) và imul(signed multiply)
  
   7. interger-division : Please compute the following:
  
   speed = distance / time, where:
  
   distance = rdi
  
   time = rsi
  
   speed = rax
  
   Note that distance will be at most a 64-bit value, so rdx should be 0 when dividing.
  
   Solution : mov rax, rdi
  
              div rsi
  
   Note : Lưu í về cách sử dụng div:   
   https://www.tutorialspoint.com/assembly_programming/assembly_arithmetic_instructions.htm

   div là phép chia tạo ra 1 thương và 1 phần dư.

   8. modulo-operation: Please compute the following: rdi % rsi
  
   Solutions:
  
   mov rax, rdi
   div rsi
   mov rax, rdx
  
   Note: giữa interger-division và modulo-operation, ta có thể thấy 2 cái đều giống nhau vì là phép tính chia, nhưng sẽ khác nhau vì ở modulo-operation được tạo 
   ra để lấy phần dư, để hiểu thêm về div và phép chia hãy đọc lại và so sánh kĩ 2 bài này.

   9. set-upper-byte :Using only one move instruction, please set the upper 8 bits of the ax register to 0x42.

   Solutions : mov ah, 0x42

   Note: Ban đầu mình làm là mov ax, 0x42 nhưng giá trị sai vì hình như ax là 0x0 theo như lỗi ở trong linux, và để í cái bảng, thì lower 8 bits sẽ là al, vậy nên 
   upper 8 bits sẽ là thanh ghi ah.

   10. 

