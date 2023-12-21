# Bomb-Lab-phases-solutions
# Tools used
* IDA deassembler
* gdb debugger
# Phase 1
Below is the assembly code for function ```phase_1```
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/0f151010-ca0f-4040-ba32-c99dbf631bc1)
- line ```400ee9```:

  we call function named ```string_not_equal```, so we conclude that the input consists of some kind of string which needs to be identical with the string saved in ```%esi``` register ,as shown in line ```400ee4```.
  
Now we need to know what string is saved in that memory address of ```%esi``` and it's our answer for first phase.

we can use the command 

```x\s $esi``` 

in ```gdb``` to extract the value of memory address in ```%esi``` register.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/49cb516c-a840-4012-b8e4-5920064333ea)

The image above show our answer for ```phase 1```.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/8e3fcf40-69ae-4b27-8705-17e19cceb383)

# Phase 2
Below is the assembly code for function ```phase_2```.
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/43d86e0b-f3c1-4ddc-94ca-2cd94be98c97)
- line ```400f05```:

  It shows here we are calling function ```read_six_numbers```, so our input must be inform of 6 numbers. Let see how we could know them
- line ```400f0a```:

  We compare the value stored in the memory address that ```%rsp``` holds with ```1```, and if they aren't equal, the bomb will explode!
So we need to know what value must be ```1``` in our input.

Let say we input list of numbers ```{2, 4, 6, 8, 10, 12}```

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/99206dc3-36ed-46f2-99b7-6a914d5e222e)

Now we know that ```%rsp``` holds the memory address of the first value. So, our first value must be ```1```.

Know, jump to line ```400f30```.

- line ```400f30```:

  ```%rbx``` now holds the value of the next value in our input.
- line ```400f17```:

  Regirster ```%eax``` holds the first value of our input, we add ```%eax``` with itself, in other words multiplying the value in register ```%eax``` by ```2```, and compare with the second value in our input, which in our case must be ```2```, and keep doing the same for the other six number.
  
So, our answer for this phase must be like that:

  Begin with ```1```, each other value will be two times its previous value.
then, the answer for ```phase 2``` is : ```{1, 2, 4, 8, 16, 32}```

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/9b236dcc-3767-43ae-8bae-ab212cb353c1)

 # Phase 3
 Below is the assembly code for function ```phase_3```.
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/853d554f-41f4-4d5a-8be9-08512dc4393e)

- line ```400f47``` and line ```400f4c```:
-   We can see that we are moving value stored in memory address of ```%rsp``` register with some offset values to both ```%rcx``` and ```%rdx``` registers.
- line ```400f60```:
-   calling ```scanf``` function returns number of arugments which are succesfully read from the user. Saving the return value in ```%eax``` and comparing it with ```1``` to be greater than, which deduces that the input must be more than one input 

let's assume we input three number: ```{4, 2, 7}``` to discover where are they stored

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/0a7cdba3-409b-4aa2-a496-af7d9990d494)

now we can deduce from line ```400f6a``` that we are comparing our first input with ```7```, and the ```ja``` instruction indicates that our value neither negative nor greater than 7.

So, our first value is in range from 0 to 7.

-line ```400f71```:
  register ```%rax``` now holds the value of the first input.
- line ```400f75```:
  we jump to spcific line depending of the value of our input scaling by ```8```.

  we can see the current instruction from our debugger
  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/fed2e607-eac4-4787-9211-36c9b0e5e6a6)
  now ```%eax``` holds ```0x185``` value in hex which is equivilant to ```398```.
- line ```400fbe```:
  we compare ```0xc(%rsp)``` with the value we stored in ```%rax```.

  let's see what ```0xc(%rsp)``` holds:

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/da42856b-7251-4653-9a05-6789e8e5f781)
  we can see it holds our second input.

  So, to not let the bomb explode we should have our next input equals ```398```.
Now, we have a clear vision about ```phase_3``` code.

it actually implements a switch case with having our first input as the case number,  and the second value holds the value which stored in ```%eax``` register

if we went through all the ```8``` possible values we will get the following valid solutions for ```phase 3```:
  

  




