# Bomb-Lab-phases-solutions
# Tools used
* IDA deassembler
* gdb debugger

***
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

***
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

***
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

- line ```400f71```:
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

it implements a ```switch case``` while having the first input as the case number,  and the second value holds the value which stored in ```%eax``` register.

if we went through all the ```8``` possible values we will get the following valid solutions for ```phase 3```:

| first input | second input | line excuted  |
| :---------: | :----------: | :----------:  |
| 0           | 207          |  ```400f7c``` |
| 1           | 311          |  ```400fb9``` |
| 2           | 707          |  ```400f83``` |
| 3           | 256          |  ```400f8a``` |
| 4           | 389          |  ```400f91``` |
| 5           | 206          |  ```400f98``` |
| 6           | 682          |  ```400f9f``` |
| 7           | 327          |  ```400fa6``` |

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/c10ec62f-ec75-48ae-b57a-fff90243ade8)


***
# phase 4
Below is the assembly code for ```phase_4```
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/6316e5af-ceba-4d70-bc6c-9bd3b2f2b82a)

As usual, we can see same instructions applied for ```Phase_4``` in terms of input, the only different here in line ```401029```. We can see the next line instuction ```jne``` indicates that this time we must only input ```2``` integers ,```phase_3``` has no restrictions on the number of input arguments except, they must be more than one input.

- line ```40102e```:
    We now that ```0x8(%rsp)``` holds the value of the first input, as before in ```phase_3```, and it also must be below than or equal to ```$0xe``` which is ```14```. So, first argument must have value in range ```0 - 14```.

- From line ```40103a``` till line ```401044```:
   Registers ```$%edx``` and ```%esi``` holds the values ```14``` and ```0``` respectievly. Register ```%edi``` holds our first input.
- line ```401048```:
   We are calling some function called ```func4```, before going in details of ```func4``` implementation, we can notice in line ```40104d``` that we test the value of ```%eax``` with itself, and if the value isn't equal to zero, the bomb will explode. So, in order to have keep the bomb unexploded, either the return value of ```func4``` must be zero, or we do some operations which must lead to have ```0``` in ```%eax``` register.

Below is the assembly code for ```func4```.
![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/01e01051-50d5-49e1-87af-eb952920a46f)

The function details is quite confusing, but we know there is recursion in our function. Let's focus on our main goal to have ```%eax``` holding value ```0```.

We can see that line ```400ff2``` moving ```0``` to ```%eax```, then we must know how to lead the function to go there.

- line ```400fd2``` till ```400fe2```:
  
  There are some operations on our registers.
    - Subtracting value stored ```%esi``` from ```$edx``` and storing in ```%eax```, which is actually ```14```, because we set intial values to them before calling ```func4```.
    - Afterwards, ```%ecx``` holds our value, shifting it right ```0x1f``` times, which equals to ```31```. In other words, resetting the value of ```%ecx``` to ```0```.
    - Shifting right ```%eax``` on time, dividing by ```2```.
    - Add ```%eax``` to ```%rsi``` and saving in ```%ecx```.
      
  Actually, these operations seems fimiliar:
    - We set a ```low``` and ```high``` variables, ```%esi``` and ```edx```.
    - we subtract ```low``` from ```high``` and divide by ```2```.
    - add ```low``` again.
  
  This is actually how we get the ```mid``` variable in ```Binary Search``` algorithm.

  ```mid = low + (high - low) / 2;```
  So, in fact, ```func4``` implements a ```binary search``` algorithm, and ```$ecx```, in other words the ```mid``` variable, holds ```7``` in the first iteration.

- line ```400fe2```:
  comparing the first input stored in ```%edx``` with the ```mid``` value, which is ```7```.
  If it's less than or equal we jump to the part where we set ```%eax``` to ```0```.
- line ```400ff7```:
  we compare our first input again with ```%esi```, which is the ```mid```. If the value is greater or equal, we succssfully end our function and returns with ```%eax``` = ```0```

So by having our first input equals to ```7```, we can easily reach the end of our function, having ```%eax``` equals to ```0```, which we intented from the beginning, returning to function ```phase_4```.

- back to line ```40104d```:
  here we said we compare ```%eax``` with itself, and since value in ```%eax``` is ```0``` now, the bomb won't explode until now.
- line ```401051```:
  this line actually is very straight forward. We compare our second input with ```0```, and the bomb will explode if they aren't equal. So, our second input must be ```0```.

So an answer which is valid to solve ```phase 4```, is ```7 0```.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/cf5a820b-c5a0-433b-8bac-e51fc3ee957a)

<details>
    <summary>Bonus</summary>
    
There are actually four values for the first input that also solves ```phase 4```, try guessing them!

<details>
  <summary>Hint</summary>

Try coding this function in any language, and choose the values that always validiate the first codition in line ```400fe4```.

</details>

<details>
  <summary>Solution</summary>

Always aim to have the first condition in line ```400fe4``` true, that will lead always register ```%eax``` to have ```0```, if we carefully tried some values, we can see that ```0 1 3 7``` always trigger the left subtree. 

**Why specifically these values work?**

As we shift right each time by ```1```, ```mid``` will have first value of ```7``` then ```3``` then ```1``` and last value will be ```0```.

</details>

</details>

***
# phase 5

Below is the assembly code for ```phase_5```

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/d507aaa4-0ed2-4bf8-8e89-61eb7340eb23)

- line ```40107a```:
  Calling function ```string_length``` indicates that our input consists of string and we calculate its length.


- line ```40107f```:
  comparing the input string's length with ```6```, this implies our input consists of 6 chars, otherwise the bomb will explode, now we jump to line ```4010d2```.

let's try some string like ```"abcdef"```

- line ```4010d2```:
  Setting ```%eax``` back to ```0```, and jump to line ```40108b```.
- line ```40108b```:
  it seems we are moving byte address to register ```%ecx```. let's see what is stored now in ```%ecx```.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/c3b93ffc-9b63-463c-9f15-1ee365ebc8d6)
  
It's clear that ```$ecx``` holds our first character, which means this is a loop which iterates over our input string character by character.

- line ```401096```:
  our character is saved in register ```%edx```, ANDing with ```0xf```, ```15``` in decimal, means we deduce the first 4 bits in our character. In case of ```a```,  the value is ```1```.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/d5d22517-ee87-4f0e-bc56-cf15a718e623)

- line ```401099```:
  we move value stored in some memory address with offset value in ```%rdx``` to ```%edx```. Let's see what value now stored in ```%edx```.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/16002b17-154c-4bc1-af91-45335719d5a4)

  So it seems we didn't do nothing, but consider the value stored in ```%edx``` in the second iteration.
  
  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/58b8cd89-e3af-4554-9178-38ff71ba2c0c)

  now it seems we figured our what is happening. This is actually an encoding process, where we input a character and map it to another character stored in array at some address.

- in line ```4010a0```:
  we store these character in another address.

- line ```4010bd```:
  we call ```strings_not_equal``` function, so now we know what ```phase 5``` does.
    - we input string.
    - encode each character to another character.
    - and check if our encoded string match the stored string.

So, in order to solve this mystery, we must know what is the stored string, and begin a decode process to retrive the input and that's the answer for this phase.

We can use command ```x /c 0x40204b0 + "index" ``` to get each character.

here is an example:

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/e100efd7-1c9d-4047-badf-ab9098c000ae)

  
Below is the character stored in each index of the econding array:
| index  | character |
| :---:  | :-------: |
| 0      | 'm'       |
| 1      | 'a'       |
| 2      | 'd'       |
| 3      | 'u'       |
| 4      | 'i'       |
| 5      | 'e'       |
| 6      | 'r'       |
| 7      | 's'       |
| 8      | 'n'       |
| 9      | 'f'       |
| 10     | 'o'       |
| 11     | 't'       |
| 12     | 'v'       |
| 13     | 'b'       |
| 14     | 'y'       |
| 15     | 'l'       |

in line ```4010b3```, memory address for the stored string is ```0x40245e```. So, we can know each character with the same instruction we did to get but change the address value.

Example:

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/f3e60539-5bbc-43bc-9af5-93c882880451)

Below is the characters of the stored string:

| index  | character |
| :---:  | :-------: |
| 0      | 'f'       |
| 1      | 'l'       |
| 2      | 'y'       |
| 3      | 'e'       |
| 4      | 'r'       |
| 5      | 's'       |

Now decoding each character

| index  | character | decoded character index in the array | encoded character |
| :---:  | :-------: | :----------------------------------: | :---------------: |
| 0      | 'f'       | 9                                    | 'i'               |
| 1      | 'l'       | 15                                   | 'o'               |
| 2      | 'y'       | 14                                   | 'n'               |
| 3      | 'e'       | 5                                    | 'e'               |
| 4      | 'r'       | 6                                    | 'f'               | 
| 5      | 's'       | 7                                    | 'g'               |

So out answer is string ```"ionefg"```.

For completeness, any case, either cpaital or small, for any letter will work as long it's the same character.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/a7d54429-8dbd-4fe8-beff-3878d27d240e)

***

# phase 6






  




  

  

  




