---
title: RootMe Cracking Part 1
date: 2023-11-06 
categories: [RootMe, Cracking]
tags: [RootMe, RootMe - Cracking, WriteUp]
author: barabulkit
---

# Elf x86 - 0 protection

In this challenge we can get password right from the main function
![ch1password](/assets/img/rootme/cracking/ch1.png)

# Elf x86 - Basic

Same as the previous one
![ch2password](/assets/img/rootme/cracking/ch2.png)

# PE x86 - 0 protection

First we can search for strings and find strings that looks interesting
![ch15strings](/assets/img/rootme/cracking/ch15strings.png)

Then we can find where they are used by looking at them in code
![ch15stringsrefernce](/assets/img/rootme/cracking/ch15stringsrefernces.png)

and then we get password `SPaCIoS` in this function
![ch15password](/assets/img/rootme/cracking/ch15password.png)

# ELF C++ - 0 protection

I started with main function
```c
undefined4 main(int param_1,char **param_2)

{
  char *pcVar1;
  bool bVar2;
  basic_ostream *this;
  undefined4 uVar3;
  allocator<char> local_1e;
  allocator<char> local_1d;
  basic_string local_1c;
  basic_string<char,std--char_traits<char>,std--allocator<char>> local_18 [4];
  basic_string<char,std--char_traits<char>,std--allocator<char>> local_14 [4];
  undefined4 *local_10;
  
  local_10 = &param_1;
  if (param_1 < 2) {
    pcVar1 = *param_2;
    this = operator<<<std--char_traits<char>>((basic_ostream *)cerr,"usage : ");
    this = operator<<<std--char_traits<char>>(this,pcVar1);
    this = operator<<<std--char_traits<char>>(this," password");
    operator<<((basic_ostream<char,std--char_traits<char>> *)this,
               _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
    uVar3 = 5;
  }
  else {
    allocator();
                    /* try { // try from 08048b0e to 08048b12 has its CatchHandler @ 08048c64 */
    basic_string((char *)local_14,(allocator *)&DAT_08048dc4);
    allocator();
                    /* try { // try from 08048b33 to 08048b37 has its CatchHandler @ 08048c3b */
    basic_string((char *)local_18,(allocator *)&DAT_08048dcc);
                    /* try { // try from 08048b4c to 08048b50 has its CatchHandler @ 08048c1d */
    plouf((basic_string)&local_1c,(basic_string)local_18);
                    /* try { // try from 08048b5a to 08048b5e has its CatchHandler @ 08048c2c */
    ~basic_string(local_18);
    ~allocator(&local_1e);
                    /* try { // try from 08048b70 to 08048b74 has its CatchHandler @ 08048c55 */
    ~basic_string(local_14);
    ~allocator(&local_1d);
                    /* try { // try from 08048b92 to 08048c08 has its CatchHandler @ 08048c7b */
    bVar2 = operator==<char,std--char_traits<char>,std--allocator<char>>(&local_1c,param_2[1]);
    if (bVar2 == false) {
      this = operator<<<std--char_traits<char>>((basic_ostream *)cout,"Password incorrect.");
      operator<<((basic_ostream<char,std--char_traits<char>> *)this,
                 _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
    }
    else {
      this = operator<<<std--char_traits<char>>
                       ((basic_ostream *)cout,
                        "Bravo, tu peux valider en utilisant ce mot de passe...");
      operator<<((basic_ostream<char,std--char_traits<char>> *)this,
                 _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
      this = operator<<<std--char_traits<char>>
                       ((basic_ostream *)cout,"Congratz. You can validate with this password...");
      operator<<((basic_ostream<char,std--char_traits<char>> *)this,
                 _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
    }
    uVar3 = 0;
    ~basic_string((basic_string<char,std--char_traits<char>,std--allocator<char>> *)&local_1c);
  }
  return uVar3;
}
```

here we can see `Congratz. You can validate with this password...` string, its our aim
in order to reach this output we need to pass condition
```c
bVar2 = operator==<char,std--char_traits<char>,std--allocator<char>>(&local_1c,param_2[1]);
if (bVar2 == false) {
```
it compares string local_lc and param2[1], where param2 is programm arguments array, so its our input
we need to understand what is local_lc
we can see pointer to it in plouf func call
```c
plouf((basic_string)&local_1c,(basic_string)local_18);
```
lets take a look at this function
```c
basic_string plouf(basic_string param_1,basic_string param_2)

{
  byte bVar1;
  uint uVar2;
  byte *pbVar3;
  uint uVar4;
  char *pcVar5;
  basic_string<char,std--char_traits<char>,std--allocator<char>> *in_stack_0000000c;
  allocator<char> local_21;
  uint local_20;
  
  allocator();
                    /* try { // try from 080489b6 to 080489ba has its CatchHandler @ 08048a4b */
  basic_string((char *)param_1,(allocator *)&DAT_08048db0);
  ~allocator(&local_21);
  local_20 = 0;
  while( true ) {
    pcVar5 = (char *)operator[]((basic_string<char,std--char_traits<char>,std--allocator<char>> *)
                                param_2,local_20);
    if (*pcVar5 == '\0') break;
                    /* try { // try from 080489dc to 08048a3c has its CatchHandler @ 08048a62 */
    pbVar3 = (byte *)operator[]((basic_string<char,std--char_traits<char>,std--allocator<char>> *)
                                param_2,local_20);
    uVar2 = local_20;
    bVar1 = *pbVar3;
    uVar4 = length();
    pbVar3 = (byte *)operator[](in_stack_0000000c,uVar2 % uVar4);
    operator+=((basic_string<char,std--char_traits<char>,std--allocator<char>> *)param_1,
               *pbVar3 ^ bVar1);
    local_20 = local_20 + 1;
  }
  return param_1;
}
```

in next code we can see cycle until `\0` (end of string of second param)
```c
while( true ) {
    pcVar5 = (char *)operator[]((basic_string<char,std--char_traits<char>,std--allocator<char>> *)
                                param_2,local_20);
    if (*pcVar5 == '\0') break;
```

and that `param1` (that is local_1c string we are looking for) is made from xor of `pbVar3` and `bVar1`
```c
operator+=((basic_string<char,std--char_traits<char>,std--allocator<char>> *)param_1,
               *pbVar3 ^ bVar1);
```

from next code we understand that `bVar1` is just a symbol at current index from param2 (local_18)
```c
pbVar3 = (byte *)operator[]((basic_string<char,std--char_traits<char>,std--allocator<char>> *)
                                param_2,local_20);

...

bVar1 = *pbVar3;
```

and `pbVar3` is equal to symbol in some string at index equal to current cycle counter divided with modulo by length of that string
```c
uVar4 = length();
pbVar3 = (byte *)operator[](in_stack_0000000c,uVar2 % uVar4);
```

the problem is that i have no idea what is `in_stack_0000000c` string in ghidra's disassembler output, so i looked at `plouf` call in assembly

![plouf_call](/assets/img/rootme/cracking/plouf_call.png)
we can see there that `local_14` is also passed to `plouf`, so i guessed that `in_stack_0000000c` is `local_14`

Now we need to find content of `local_18` and `local_14` and we almost done

```c
basic_string((char *)local_14,(allocator *)&DAT_08048dc4);

...

basic_string((char *)local_18,(allocator *)&DAT_08048dcc);
```

there we can see that `local_14` is at `DAT_08048dc4` and `local_18` at `DAT_08048dcc`
we can navigate to that data in hydra 
![dats](/assets/img/rootme/cracking/dats.png)

now we can just reproduce all this steps with this strings in python and get password
```python
bytes1 = [0x18, 0xd6, 0x15, 0xca, 0xfa, 0x77]
bytes2 = [0x50, 0xb3, 0x67, 0xaf, 0xa5, 0x0e, 0x77, 0xa3, 0x4a, 0xa2, 0x9b, 0x01, 0x7d, 0x89, 0x61, 0xa5, 0xa5,
0x02, 0x76, 0xb2, 0x70, 0xb8, 0x89, 0x03, 0x79, 0xb8, 0x71, 0x95, 0x9b, 0x28, 0x74, 0xbf, 0x61,
0xbe, 0x96, 0x12, 0x47, 0x95, 0x3e, 0xe1, 0xa5, 0x04, 0x6c, 0xa3, 0x73, 0xac, 0x89]
res = []

for i, el in enumerate(bytes2):
    tmp = i % len(bytes1)
    el2 = bytes1[tmp]
    res.append(chr(el ^ el2))

print(''.join(res))
```

after executing this script will get password `Here_you_have_to_understand_a_little_C++_stuffs`
![ch25password](/assets/img/rootme/cracking/ch25password.png)