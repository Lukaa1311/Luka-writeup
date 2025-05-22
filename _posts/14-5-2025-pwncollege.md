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

   Solutions : mov a   h, 0x42

   Note: Ban đầu mình làm là mov ax, 0x42 nhưng giá trị sai vì hình như ax là 0x0 theo như lỗi ở trong linux, và để í cái bảng, thì lower 8 bits sẽ là al, vậy nên 
   upper 8 bits sẽ là thanh ghi ah.

   10. efficient-modulo : Please compute the following:
       
   rax = rdi % 256
   rbx = rsi % 65536

   Solution : mov al, dil
              mov bx, si
   Note : đọc kĩ gợi í của bài, có 1 chút math trick : 256 = 2^8, 65536 = 2^16 -> số mũ là số lower của số bits -> 1 byte = 8 bit nên quy đổi về bảng các thanh ghi để lựa chọn thanh ghi đúng, ở

   trường hợp này rax là al, rdi là dil, rbx là bx, rsi là si.

   11. byte-extraction : Please perform the following: Set rax to the 5th least significant byte of rdi. 

   For example:

   rdi = | B7 | B6 | B5 | B4 | B3 | B2 | B1 | B0 |
  
   Set rax to the value of B4

   Solution :

   shr rdi, 32         ; 5th least significant byte là byte số 4, đếm từ 0, ô trống sẽ là | 0 | 0 | 0 | 0 | B7 | B6 | B5 | B4 | 

   mov al, dil         ; al là bit thấp nhất của rax (byte0) và dil là bit thấp nhất của rdi (byte 0)

   12. bitwise-and : Without using the following instructions: mov, xchg, please perform the following:

   Set rax to the value of (rdi AND rsi)

   Solution :
  
   and rdi, rsi          ; thực hiện phép toán and 

   lea rax, [rdi]        ; lea chuyển địa chỉ của toán hạng nguồn đến với toán hạng đích, để í dấu [] vì có 2 ngữ cảnh khác nhau với giá trị khác nhau nếu có ngoặc hoặc không có ngoặc.

   13. check-even : Using only the following instructions:

   and
  
   or
  
   xor
  

  Implement the following logic:

  if x is even then
    y = 1
  else
    y = 0
  Where:
  
  x = rdi

  y = rax

  Solution :

  Note : https://www.tutorialspoint.com/assembly_programming/assembly_logical_instructions.htm , đọc hiểu kĩ về các thanh ghi logical trong assembly

  Logic đoạn code vận hành : x chẵn => bit thấp nhất của x = 0 (bit 0)
                             x lẻ => bit thấp nhất của x = 1 
  Với yêu cầu đề bài x = 0 => y = 1 và ngược lại 
  
  Code solution :

  and rdi, 1           ; giữ lại bit thấp nhất của rdi, các bit khác thành 0

  xor rax, rax         ; clear rax về 0(số nào xor với chính nó cũng bằng 0)

  xor rdi, 1           ; đảo bit thấp nhất của thanh ghi rdi, 0 thành 1, 1 thành 0

  or rax, rdi          ; gán rax = rdi ( 0 hoặc 1)

  13. memory-read : Please perform the following: Place the value stored at 0x404000 into rax. Make sure the value in rax is the original value stored at 0x404000.
 
  Solution : mov rax, [0x404000]   ; lưu ý ở đây là dấu [] thể hiện cho memory access, đọc giá trị từ bộ nhớ tại địa chỉ 0x404000 vào thanh ghi rax.

  14. memory-write : Place the value stored in rax to 0x404000

  Solution : mov [0x404000], rax  ; khác với bài trên, bài này là ghi giá trị trong thanh ghi rax vào bộ nhớ tại địa chỉ 0x404000.

  15. memory-increment :
  Place the value stored at 0x404000 into rax.

  Increment the value stored at the address 0x404000 by 0x1337.
  
  Make sure the value in rax is the original value stored at 0x404000 and make sure that [0x404000] now has the incremented value.

  Solution :

  mov rax, [0x404000]      
  
  mov rbx, 0x1337
  
  add [0x404000], rbx 

  Challenge noting : tuy là đã nắm rõ khái quát về memory access và memory write, nhưng vẫn rất dễ nhầm lẫn bởi thứ tự khi khai báo thanh ghi trên câu lệnh. 
  ví dụ nếu mov rax, [0x404000] thì sẽ là đọc giá trị tại bộ nhớ 0x404000 vào thanh ghi rax, còn lệnh add [0x404000], rbx là cộng trực tiếp vào vùng nhớ chứ
  không phải cộng vào thanh ghi.

  16. byte-access : Set rax to the byte at 0x404000.

  Solution: mov al, [0x404000] ; đọc 1 byte từ bộ nhớ tại địa chỉ trên và lưu vào thanh ghi al.

  17. memory-size-access :
  
  Set rax to the byte at 0x404000

  Set rbx to the word at 0x404000
  
  Set rcx to the double word at 0x404000
  
  Set rdx to the quad word at 0x404000

  Solution : Đã có công thức sẵn về byte, word, double word và quad word, chỉ cần tìm đúng những thanh ghi tương ứng là được ( xem bảng thanh ghi trong note NASM của 5o1z)
  
  mov al, [0x404000]
  
  mov bx, [0x404000]
  
  mov ecx, [0x404000]
  
  mov rdx, [0x404000]

  18. little-endian-write : Using the earlier mentioned info, perform the following:

  Set [rdi] = 0xdeadbeef00001337
  
  Set [rsi] = 0xc0ffee0000

  Solution : ở đây chúng ta sử dụng lệnh movq ( lệnh di chuyển dữ liệu dạng 64-bit/ quadword):

  movq rax, 0xdeadbeef00001337
  
  movq [rdi], rax
  
  movq rbx, 0xc0ffee0000
  
  movq [rsi], rbx

  19. memory-sum : Perform the following:

  Load two consecutive quad words from the address stored in rdi.
  
  Calculate the sum of the previous steps' quad words.
  
  Store the sum at the address in rsi.

  Solution:
  
  movq rax, [rdi]

  movq rbx, [rdi + 8]   

  addq rax, rbx   ; tính tổng sum với 2 giá trị vừa lưu trữ ở trên 

  movq rax, rsi

  20. 
