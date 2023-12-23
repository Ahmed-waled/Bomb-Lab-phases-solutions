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
<details>
  <summary>motivation</summary>
  Before going through this phase, I should mention that this phase was the hardest one among all the phases. If you didn't manage to solve it on your own, that's very normal. I myself had a hint to help me through this phase. Any part you have reached is a great achievment. So  don't feel sad or demotivated. You did a great work until now :)!  
</details>

Before we began, as this phase was very long. I spliited it into 5 functions. Each time exaplaining the next function will be attached with its assembly to not get distracted among this phase.

Let's begin with the input part along with the first function.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/f33c872a-9d9c-4505-bf46-b95b1727e992)

- line ```401106```:

  As we have seen before in ```phase 2```, function ```read_six_number``` again is called which indicates our input to be six integers numbers.

- line ```401117``` till ```401121```:
- 
  Moving some value to ```%eax```, Subtracting ```1``` from it, then comparing to be below or equal to ```5```, the comparison is explained as follows:

   ```0``` $\le$ ```%eax - 1 ``` $\le$ ```5```

  simplidied:

  ```1``` $\le$ ```%eax``` $\le$ ```6```

So, this gives us a hint that some value should be between ```l``` and ```6```. Let's continue to find out which value should have this property.

- we now jump to line ```401128```:

  in line ```40110e```, we setted the value in ```%r12d``` to ```0```, now we increase ```1```, so now it holds ```1```. comparing it with ```6```, in the next instruction, indicates it some kind of loop that will iterate ```6``` times. So, maybe we can guess it iterates over our input.

- beginning from line ```401132``` till ```40114b```:

  line ```401132```:

    we move our current loop iterator ```%r12d``` to ```%ebx```.
  line ```401135```:

  we move our current iterator to ```%eax```, and then we acssess some value with index scale and move it to ```%eax```,
  line ```40113b``` and ```40113e```:
    It checks the current value we indexed to be not equal with value stored at ```%rbp```.
since we dont understand much what values stored here and there.

let's input some ```6``` values say: {2,5,4,1,1,6}, and see what the debugger holds now in these registers.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/81593da5-9703-4e8c-affb-a66efa2ca287)

Now, we know what is happening, ```%rbp``` holds the value of our first input, and ```%rax``` holds the next one. This checkes that both values are different. let us continue on more iteration, and see the values again.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/15d7ef66-fbad-4531-87fa-3dede2cf0cc6)

Now, ```%rax``` holds the first value, while ```%rbp``` remains with the first one. So, this loop checks if all the other five inputs are different from our first value.

now let's jump out off this loop.

- line ```40114d```:

  we increased some value in ```%r13``` register by ```4```, kinda specific value?

- line ```401151```:

  we get back to the start again!
  
- we jump now back ti line ```401114```:

  Here, when we moved the value of ```%r13``` to ```%rbp```, let's see what is ```%rbp``` holding now

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/c716aedc-4eca-42a4-af89-3009ec89bb01)

  It holds our second input, and again it checks if it's between our range from ```1``` to ```6```. Afterwards, we deep into again the same loop at ```401132```, where now we check that each value, which index bigger than current index, is different from ```5```, the value at the current index.

And again we move to the third value and do the same, the forth, and lastly the fifth value where we check it's different from the sixth value.

finally the sixth value has no value after it, wo it only checks its range.

this loop can be written as pseudo code like below:

```
for i from 0 to 6:
  for j from i + 1 to 5:
    if arr[i] == arr[j]:
      call explode_bomb();
```

So, we now have a clear vision about our input.
  - first each value must be in range from ```1``` to ```6```.
  - it must be all unique that is each value is different from all other value.

In other words, our input forms a permutation of length ```6```. (a permutattion of length ```n``` is a sequence from integers that each value from ```1``` to ```n``` appears one time.)

So our previous input example will explode our bomb.

let's try another example of permutation ```6```, let say from ```1``` to ```6``` in increasing order like this: {1, 2, 3, 4, 5, 6}.

> I guess we know now that our input is consists of array of 6 number, so each time we iterate we will only need the address for the first number. So any loop which uses index scale, it's kikely to get the value of some index.

Now we have completed our first function, let's move on to the next one.

Here is the assembly code for it.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/ff4d194b-359f-412a-8385-511b247415d4)

Before going through let's see what ```%rsp``` holds:

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/cae7e4a5-edbc-427f-970f-e5ef546f335f)

and since we have 6 input of integers we need ```4``` bytes for each one. So, last index will be $4 .\dot 6$ which equals ```24``` in decimal, ```0x18``` in hex. So, in fact, line ```401153``` moving memory address where our array should stop. This will help us later saving some time figuring out what this registers holds.

- line ```401158```:
  moving value from ```%r14``` to ```%rax```. let see what it holds:

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/1c90d097-797a-432f-969f-9d2de677aca7)

  It hold the memory address for the beginning of the array.

- line ```40115b``` till ```401166```:
  - moving ```7``` to ```%ecx```.
  - subtract ```%ecx``` from value in memory address of ```%rax```, which is ```1``` in our current iteration.
  - rewrite the calculated value in the same memory address, the current index.
  - move ```%rax``` to the next index memory address.
let's visulaize it using the debugger:

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/49d8eb81-a5ee-47b1-a4fe-ed3e1fca7935)

We can notice that before substracting the value is ```1```, as it's our input. Then, it becomes ```6```, ```7 - 1 = 6```. Which in fact, get complemented to ```7```.

By doing to each value, we actually change our input to its complement to ```7```, here is our array after this function.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/113d4eba-4877-4246-8a2d-f35d507e5aeb)

Below is the assembly code for the next function

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/59688bd6-dce9-4b05-b392-2ed83f921e7a)

- line ```40116f```:

  resetting ```%esi``` back to ```0```.

- line ```401197```:

  Again, we said that ```%rsp``` holds memory address for our array. So, we just indexing our array. ```%ecx``` holds our value at that index.

- line ```40119d```:

  either our value equals to ```1```, so we jump to the current instruction, or we continue normally.

Let's iterate two times, first time with value not equal to ```1``` and the other time is ```1```.

if our value not equal to ```1```:
  - line ```400119f``` and ```4011a4```:
    seting ```%eax``` with ```1```, and moving some memory address to ```%edx```.

    Let's see what ```%edx``` holds:
    
      ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/396950ce-2e22-4449-a934-9b47e282c846)

    Now, this is intersting. There is a label ```node1``` in that memeory address. The only nodes we can think of is some kind of linked list strcutre. So is that really is?

    If it's a linked list, then we constructed it using ```strcut```, that consists of pointer and integer, We know that pointers holds ```8``` bytes of memory,  and integer requires ```4``` bytes. So we need ```12``` bytes to move?

    Actually no, because in struct we don't add bytes, struct is organize in memory by technique called ```padding```, you can refer to this [Padding in c]([https://pages.github.com/](https://www.javatpoint.com/structure-padding-in-c#:~:text=Structure%20padding%20is%20a%20concept,create%20a%20user%2Ddefined%20structure.)https://www.javatpoint.com/structure-padding-in-c#:~:text=Structure%20padding%20is%20a%20concept,create%20a%20user%2Ddefined%20structure.).

  So in fact we need ```16``` bytes to be able to reach the second node.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/508a8530-82ce-4b18-aa42-0364c9a4f064)

  So, in fact, it's a linked list structure. Let's head back to our code.

  Now ```%edx``` holds memory address for the first node.

- line ```401176``` to ```40117d```:

  we are iterating over our nodes in the linked list and stop after we reach the i-th node (ex. if ```%ecx``` holds value ```6```, so we will stop at the sixth node.)

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/3c75db47-6107-4f86-b357-24d6350af7d8)


- line ```401188```:
  We know move the address of the current node to memory address which is value equals:

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/28b934a1-5775-4ba2-a49d-3c90e7aacaf5)

  (let us preserve this address for now).
  
  We, then, iterate over the next index of the array. We will actually keep doing the same, accessing the i-th node, moving their address to another address with some order, for each value not equal to ```1```

Let's see if the value equals to ```1```:
  - at line ```40119a``` where we check, we jump to line ```401183```:
    there we also get the address for the first node, and move it to some address. therefore we did the same without actually going through the loop.

Now as a summary for this function, there are some linked list structure where, according to out input, we start to move each i-th node to new addresses, keeping order for their new addresses.

Below is an illustration for the new order of the nodes.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/ce2fc8cb-5776-425f-afe0-70da48d2e9ee)

Now let's move to the next function, which I think you could have guessed it.

Below is the assembly code for the next one.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/ef2c06e9-e449-4aae-9a9f-6ba142127ed3)

- line ```4011ab```:
  moving some memory address to ```%rbx```, let's see its content.

    ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/2cf4337f-8440-45f0-8346-4b3dec7554a8)

  So, it now holds the memory for ```node6```, which is actually the new address where we moved it into.

- line ```4011b0```:
  moving the same address for ```%rbx``` increased by ```0x8``` to ```%rax```.

- from line ```4011ba``` till ```4011cd```:
    - move address of ```node6``` to ```%rcx```.
    - move value of memeory address of ```%rax``` to ```%rdx```.
    - moving back the content again to ```%rcx``` with offset ```0x8```.
    - increasing memory address of ```%rax``` by ```0x8```.
  Now, we know nothinig about what is happening but let's explain with the debugger.
  we first said ```%rcx``` holds ```node6```.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/47e52715-8451-401e-a509-4cb031712395)

  after we excute the reamining instructions we can see now ```%rcx``` holds ```node5```.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/2eb6821e-0055-484e-bef8-7a8958651bbe)

  So, in fact, this loop , if you have guessed, is just linking the nodes again with the new structe of them.

  below is the illustration of our new linked list.

  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/4323ecaa-9b2a-429a-ae59-86614797cbbb)

Now, our new linked list is linked, with the order of our input. Now let's continue to our final function.

![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/392fbc87-7b02-4d2f-969f-527266d87a82)

The last part of our journey 😌

- begeinning from line ```4011da``` till ```4011e9```:

  let's use the debugger showing our content
    
    - after exctuing line ```4011df``` and ```4011e3```:

      ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/70e51f92-b2f9-4380-ac2f-bcfe9896a429)

    we compare the value of ```%rbx``` with ```%eax```, ```node6``` with ```node5```, and validate that it's greater than the other otherwise, the bomb will explode.

  So, in order for the bomb not to explode, the nodes of our linked list should be ordered in some way that their values are sorted in non-increasing.

  So, that's it now we know how to solve this phase.
    - we need first to know each value in the nodes.

        remmber the memory address in line ```401183``` where it was used to access the head node, we will use it to acssess our nodes. use command ```x/d 0x6032d0 + 0x10 * (node index based-0)```.

      for example:
        
      ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/667312f2-7479-4e86-b5ba-be2411c4648b)


  | node index | node value |
  | :--------: | :--------: |
  |  1         | 332        |
  |  2         | 168        |
  |  3         | 924        |
  |  4         | 691        |
  |  5         | 477        |
  |  6         | 443        |
  
  So, in order to have linked list sorted in non-increasing order, the permutation should be: ```{3, 4, 5, 6, 1, 2}```

  There is again one missing details which menotioned in the second function. the input is complemented to ```7```.

  So, if our order should be ```{3, 4, 5, 6, 1, 2}```, so the input must be also complemented.

  So our answer for ```phase 6```, ```{4, 3, 2, 1, 6, 5}```.
  
  ![image](https://github.com/Ahmed-waled/Bomb-Lab-phases-solutions/assets/103792966/fb008deb-7f2c-471b-954a-a4741b8b7743)

  
  


