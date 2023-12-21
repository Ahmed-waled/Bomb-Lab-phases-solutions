# Bomb-Lab-phases-solutions
# Tools used
* IDA deassembler
* gdb debugger
# Phase 1
Below is the assembly code for phase1
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/0f151010-ca0f-4040-ba32-c99dbf631bc1)
- line 400ee9:

  we call function named "string_not_equal", so we conclude that the input consists of some kind of string which needs to be identical with the string saved in our memoery address ,as shown in line 400ee4.
  
Now we need to know what string is saved in that memory address and it's our answer for first phase

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/49cb516c-a840-4012-b8e4-5920064333ea)

The image above show our answer for phase 1.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/8e3fcf40-69ae-4b27-8705-17e19cceb383)

# phase 2
below is the assembly code for phase 2
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/43d86e0b-f3c1-4ddc-94ca-2cd94be98c97)
- line 400f05:

  It shows here we are calling function "read_six_numbers", so our input must be inform of 6 numbers. Let see how we could know them
- line 400f0a:

  We compare the value stored in the memory address that %rsp holds with 1, and if they aren't equal, the bomb will explode!
So we need to know what value must be 1 in our input.

Let say we input list of numbers 2 4 6 8 10 12

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/99206dc3-36ed-46f2-99b7-6a914d5e222e)

Now we know that %rsp holds the memory address of the first value. So, our first value must be 1.

Know, jump to line 400f30.

- line 400f30:

  %rbx now holds the value of the next value in our input.
- line 400f17:

  Regirster %eax holds the first value of our input, we add %eax with itself, in other words multiplying the value by 2, and compare with the second value in our input, which in our case must be 2, and keep doing the same for the other six number.
  
So, our answer for this phase must be like that:

  Begin with 1, each other value will be two times its previous value.
then, the answer for phase 2 is : 1 2 4 8 16 32

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/9b236dcc-3767-43ae-8bae-ab212cb353c1)
 




