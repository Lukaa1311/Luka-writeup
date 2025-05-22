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

  20. stack-subtraction : Using these instructions, take the top value of the stack, subtract rdi from it, then put it back.

  Solution:

  pop rax        ; lấy giá trị hiện tại trên đỉnh stack cho vào thanh ghi rax

  sub rax, rdi   ; lấy rax - rdi

  push rax       ; đẩy giá trị vừa trừ trở lại vào trong đỉnh stack

  21. swap-stack-values : Using only the following instructions:

  push
  
  pop
  
  Swap values in rdi and rsi.

  Solution :

  push rdi    ; đấy giá trị từ top stack vào rdi trước, ví dụ bắt đầu là rdi = 2
  
  push rsi    ; tương tự đẩy vào rsi trước, ví dụ rsi = 5
  
  pop rdi     ; swap, lấy giá trị từ rdi suy ra rdi = 5
  
  pop rsi     ; swap, lấy giá trị từ rsi suy ra rsi = 2 

  22. average-stack-values : Without using pop, please calculate the average of 4 consecutive quad words stored on the stack. Push the average on the stack.

  Solution :   Stack lưu 4 quad word liên tiếp 

  mov rax, [rsp]      ; quad thứ nhất tại đỉnh stack vào rsp 

  add rax, [rsp + 8]   ; quad thứ 2 +8 byte vào rax 

  add rax, [rsp + 16]  ; quad thứ 3 và thứ 4 tương tự, stack lưu 4 quad word liên tiếp, từ địa chỉ RSP đến RSP + 24 (4 quad word x 8 byte = 32 byte)

  add rax, [rsp + 24]  

  mov rbx, 4           ; đưa 4 vào rbx để thực hiện phép chia và push lại giá trị từ rax 

  div rbx

  push rax 

  23. absolute-jump : In x86, absolute jumps (jump to a specific address) are accomplished by first putting the target address in a register reg, then doing jmp 
  reg.

  In this level, we will ask you to do an absolute jump. Perform the following: Jump to the absolute address 0x403000

  Solution :
   mov rax, 0x403000  ; đẩy 0x403000 vào thanh ghi rax 

   jmp rax            ; thực hiện phép absolute jump 

  Note: Absolute jump là jump đến 1 specific address.

  24. relative-jump :
   Useful instructions for this level:

  jmp (reg1 | addr | offset)
  
  nop
  
  Hint: For the relative jump, look up how to use labels in x86.
  
  Using the above knowledge, perform the following:
  
  Make the first instruction in your code a jmp.
  
  Make that jmp a relative jump to 0x51 bytes from the current position.
  
  At the code location where the relative jump will redirect control flow, set rax to 0x1.

  Solution :    Note : https://ftp.gnu.org/old-gnu/Manuals/gas-2.9.1/html_chapter/as_7.html

  jmp target        ; nhảy đến target: 

  .rept 0x51        ; copy đoạn code lặp lại 0x51 = 81 lần 

    nop             ; no operation, để tạo ra 1 khoảng trống padding 

  .endr             ; kết thúc vùng .rept ... .endr

  target:           ; label đánh dấu vị trí nhảy đến 

  mov rax, 0x1

  25. jump-trampoline: Now, we will combine the two prior levels and perform the following:

  Create a two jump trampoline:
  
  Make the first instruction in your code a jmp.
  
  Make that jmp a relative jump to 0x51 bytes from its current position.
  
  At 0x51, write the following code:
  
  Place the top value on the stack into register rdi.
  
  jmp to the absolute address 0x403000.

  Solution: Bài này là kết hợp của absolute jump và relative jump 
  
  jmp target

  .rept 0x51

  nop

  .endr

  target :

  pop rdi

  mov rax, 0x403000    ; từ chỗ pop rdi là để tạo stack, mov rax và jmp rax là 1 absolute jump 

  jmp rax  

  26. conditional-jump : Using the above knowledge, implement the following:

  if [x] is 0x7f454c46:
  
    y = [x+4] + [x+8] + [x+12]
  
  else if [x] is 0x00005A4D:
  
    y = [x+4] - [x+8] - [x+12]
  else:
  
    y = [x+4] * [x+8] * [x+12]
  
  Where:
  
  x = rdi, y = rax.
  
  Assume each dereferenced value is a signed dword. This means the values can start as a negative value at each memory position.
  
  A valid solution will use the following at least once:
  
  jmp (any variant), cmp

  Solution:
  
  Flow chung của chương trình :
  
    So sánh eax với ELF
  
    Nếu bằng -> nhảy tới case1 (cộng)
  
   So sánh eax với PE
  
    Nếu bằng -> nhảy tới case2 (trừ)
  
   Nếu không -> thực hiện nhân
  
   Sau đó nhảy tới done


Source code: 

    mov ebx, [rdi + 4]
  
    mov ecx, [rdi + 8]
  
    mov edx, [rdi + 12]
  
    mov eax, [rdi]
  
    cmp eax, 0x7f454c46       ; so sánh eax với ELF 
  
    je case1                  ; nếu bằng nhảy đến case 1 
  
    cmp eax, 0x00005A4D       ; so sánh eax với PE
  
    je case2                  ; nếu bằng nhảy đến case 2 
  
    imul ebx, ecx             ; 
  
    imul ebx, edx
  
    jmp done
  
    case1 :
  
    add ebx, ecx
  
    add ebx, edx
  
    jmp done
  
    case 2 :
  
    sub ebx, ecx
  
    sub ebx, edx
  
    jmp done
  
    done :
  
    mov eax, ebx 

27. indirect-jump : Trước khi làm tiếp các bài về các lệnh nhảy, lưu í về kiến thức các lệnh nhảy : https://trungkmare.wordpress.com/2018/05/31/bang-lenh-nhay-trong-assembly/

Using the above knowledge, implement the following logic:

  if rdi is 0:
  
    jmp 0x403033
  else if rdi is 1:
  
    jmp 0x4030e7
  else if rdi is 2:
  
    jmp 0x4031be
  else if rdi is 3:
  
    jmp 0x40325d
  else:
  
    jmp 0x403329

Please do the above with the following constraints:

  Assume rdi will NOT be negative
  
  Use no more than 1 cmp instruction
  
  Use no more than 3 jumps (of any variant)
  
  We will provide you with the number to 'switch' on in rdi.
  
  We will provide you with a jump table base address in rsi.

Here is an example table:

  [0x4042e4] = 0x403033 (addrs will change)
  
  [0x4042ec] = 0x4030e7
  
  [0x4042f4] = 0x4031be
  
  [0x4042fc] = 0x40325d
  
  [0x404304] = 0x403329

Solution : 

cmp rdi, 3     ; so sánh rdi với 3, 3 là case lớn nhất hợp lệ 

ja case        ; jump vào case mặc định nếu rdi > 3 

jmp [rsi + rdi*8]    ; Nhảy tới địa chỉ trong bảng nhảy tại offset rdi*8 từ rsi

case: 
    jmp [rsi + 4*8]  ; Nhảy tới địa chỉ mặc định trong bảng nhảy 

28. average-loop : As an example, a for-loop can be used to compute the sum of the numbers 1 to n:

sum = 0

i = 1

while i <= n:  

   sum += i
   
   i += 1  
   
Please compute the average of n consecutive quad words, where:

rdi = memory address of the 1st quad word

rsi = n (amount to loop for)

rax = average computed  

Solution : 

xor rax, rax      ; Đặt rax = 0, dùng để tích lũy tổng các quad words

xor rbx, rbx      ; Đặt rbx = 0, biến đếm vòng lặp (index i)

sum_loop:         ; Nhãn bắt đầu vòng lặp 

add rax, [rdi + rbx*8]    ; cộng vào rax giá trị tại địa chỉ [rdi + rbx * 8]

inc rbx             ; Tăng biến đếm rbx lên 1 

cmp rbx, rsi      ; So sánh rbx với rsi 

jl sum_loop       ; Nếu rbx < rsi, nhảy lại sum_loop để cộng tiếp

div rsi           

29. count-non-zero: As an example, say we had a location in memory with adjacent numbers and we wanted to get the average of all the numbers until we find one bigger or equal to 0xff:

average = 0

i = 0

while x[i] < 0xff:

  average += x[i]
  
  i += 1
  
average /= i

Using the above knowledge, please perform the following:

Count the consecutive non-zero bytes in a contiguous region of memory, where:

rdi = memory address of the 1st byte

rax = number of consecutive non-zero bytes

Additionally, if rdi = 0, then set rax = 0 (we will check)!

An example test-case, let:

rdi = 0x1000

[0x1000] = 0x41

[0x1001] = 0x42

[0x1002] = 0x43

[0x1003] = 0x00

Then: rax = 3 should be set.

Solution : 

xor rax, rax       

mov rbx, -1        

cmp rdi, 0

je done

loop:

    inc rbx
    
    mov rcx, [rdi+rbx]
    
    cmp rcx, 0
    
    jne loop
    
    mov rax, rbx

done:

30. string-lower :

Please implement the following logic:

str_lower(src_addr):

  i = 0
  
  if src_addr != 0:
  
   while [src_addr] != 0x00:
    
   if [src_addr] <= 0x5a:
      
   [src_addr] = foo([src_addr])
        
   i += 1
        
   src_addr += 1
      
  return i 
  
  foo is provided at 0x403000. foo takes a single argument as a value and returns a value.
  
  All functions (foo and str_lower) must follow the Linux amd64 calling convention (also known as System V AMD64 ABI): System V AMD64 ABI
  
  Therefore, your function str_lower should look for src_addr in rdi and place the function return in rax.
  
  An important note is that src_addr is an address in memory (where the string is located) and [src_addr] refers to the byte that exists at src_addr.
  
  Therefore, the function foo accepts a byte as its first argument and returns a byte.

Solution : 

mov r8, 0x403000

str_lower:

   xor rcx, rcx
    
   cmp rdi, 0x0
    
   je done

   loop:
        mov rbx, rdi
        
        xor rdi, rdi
        
        mov dil, byte ptr [rbx]
        
        cmp dil, 0x0
        
        je done
        
        cmp dil, 0x5a
        
        jg greater
        
        inc rcx
        
        call r8
        
        mov byte ptr [rbx], al

    greater:
    
        mov rdi, rbx
        
        inc rdi
        
        jmp loop

    done:
    
        mov rax, rcx
        
        ret

32. most-common-byte : 

mov rbp, rsp

sub rsp, 0x100

xor r8, r8

most_common_byte:

    loop1:
    
        cmp r8, rsi
        
        jg next
        
        mov dl, byte ptr [rdi + r8]
        
        add byte ptr [rsp + rdx], 1
        
        inc r8
        
        jmp loop1

    next:
    
        xor rcx, rcx
        
        xor rbx, rbx
        
        xor rax, rax
        
        loop2:
        
            cmp rcx, 0xff
            
            jg done
            
            cmp [rsp + rcx], bl
            
            jg updateFreq
            
            jmp increment

        updateFreq:
        
            mov bl, [rsp + rcx]
            
            mov rax, rcx
            
            jmp increment

        increment:
        
            inc rcx
            
            jmp loop2

        done:
        
            mov rsp, rbp
            
            ret
