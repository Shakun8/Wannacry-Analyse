
# Wannacry ransomware Dynamique And static Analyse

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/avatars-5MJkAJDeE44fah5K-W4X6QA-t240x240.jpg?raw=true)

Hello Welcome To This new Challenge of Hackers new's bdarija

This challenge is regarding on a week of hacking , it's means that peoples need to do anything about hacking and stuff

Now let's jump into that !!

i'm literally not that good on reverse and stuff like that , that's why i take this challenge to impove my self a bit on it , i always sleep on video regarding anything related on reverse and binary exploitation and stuff like that , and always get a bit knowledge day by day

> NOW LET'S JUMP IN THE IMPORTANT THINGS !!

Today m gonna make a really simple dynamique analyse about wannacry ransomware 
as all we know wannacry is a famous ransomware , it was started on 2017 and it affect a lot of companies and peoples as well 


```
as we all know : Marcus Hutchins is the one who stopped the ransomware attack ( for peoples who want to know a little bit on this things : https://www.youtube.com/watch?v=vveLaA-z3-o&t=67s
this dude find a domaine which can killswitch all that and a lot of things as well
```

![image info](https://www.francetvinfo.fr/pictures/kPMESFvVtDj6R39eXtOAPhYQvQM/752x423/2017/08/04/phpRtC622_1.jpg)

My mission today is i will try to find this domaine as well and trying to anaylse the traffic with wireshark and seing what this ransomware can affect on the network and so one and so one

> let's give a bit understanding on how reverse work and how we can compile and decompile and dissasmble as well

okay let's imagine that we have this simple programme wrote by c
this programme simply give the result of the sum of variables

```c
#include <stdio.h>
int main() {
   int x = 1;
   int y = 5;
   int some = (x+y);
   printf("%d",some);
   return 0;
}
```
cool , now let's compile it with `gcc` , command : `gcc reverse.c -o reverse.out`
nice since we have this result of this file , so we can execute this programme
result : `6`

nice , it worked , now let's jump to reverse stuff
let's use gdb to debuge this and reverse this programme , and let's understand the assembly code as either

FIRST , let's look at the function that we have 
command : `info functions`
![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/1.JPG?raw=true)
cool we have the main function , let's disassemble it

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/2.JPG?raw=true)

LET'S udnerstand this code , relax and chill

first let's give you some a bit knowledge abt some functions in assembly
1 ) > `mov` : it gives a value to a register , for a example : `mov eax,5` : here this eax register took `5`
2 ) > `add` : it do the sum of 2 registers ( depending on the value )

cool first we have : 
```assembly
mov      DWORD PTR [rbp-0x4],0x1
mov 	 DWORD PTR [rbp-0x8],0x5
mov      edx,DWORD PTR [rbp-0x4]
mov      eax,DWORD PTR [rbp-0x8]
add      eax,edx
```
cool , let's understand this :
first we give the 0x1 which is `1` to the rbp-0x4 pointer
then we give the 0x5 which is `5` to the rbp-0x8 pointer
after that we gave the value of the first pointer to edx register
and also we give the value of the second pointer to eax register
and finally we notice that `add` function which make the sum of the `edx` and the `eax`

> NICE I HOPE U GUYS UNDERSTAND THIS PART !

now we have this 
```assembly
call   0x1030 <printf@plt>
```
simply it just call the `printf()` function .

# NOW LET'S TAKE A LOOK AT THE WANNACRY RANSOMWARE 

let's tun the command :
`strings wannacry.exe | less` :
![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/3.JPG?raw=true)

umm we can see a lot of interssted stuff here , it like some functions from windows api called , and also we can see that it loaded some dll and so one and so one

let's open this with `ollydbg`
 overview : 
 
 ![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/4.PNG?raw=true)
 
Cool since we have all this , let's search for strings and see if we can find that domaine that killswitch 
Let's see :



[![Click here](https://img.youtube.com/vi/ncegr7o8WZY/0.jpg)](https://www.youtube.com/watch?v=ncegr7o8WZY) 



cool : 
![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/5.PNG?raw=true)

domaine : `http://www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com`

let's dnslookup and see the date of the creation and the expiration : 

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/6.JPG?raw=true)

as we can see the domaine has been created on 2017 ( same year of wannacry attack ofc :D)

now let's open this with idapro and see what else we can analyse : 

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/8.PNG?raw=true)

here we have some interesting functions regarding doing some TCP connections , which can mean that connection may be lead to some reverse shell either .


# Dynamique Analyse !

First let's run execute our wannacry and let's intercept the all the traffic connections with wireshark 

after we finish we got that pcape file and this traffics : 

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/9.PNG?raw=true)

Umm we notice this smb traffic and connection and it's interested and now we need to analyse it .

let's filter on the smb protocole and see what's the important traffic

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/10.PNG?raw=true)

after a while of searching and hours , i found an interesting traffic there is some kinda shellcode injected intro that connection

i didn't stop and what comes to my mind is the eternalblue exploit ( it's the famouset vuln that can send some shellcode and overflow the buffer to get full acessse to the shell )

Well , let's see folow the stream and see what's going on here :

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/11.PNG?raw=true)
![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/12.PNG?raw=true)


> WELL HERE i've been thinking , is that what im saying is true or not? how do i can verify that this shellcode is the etternal blue shellcode that it sent

WELL GOOGLE IS OUR FRIEND XD !!

![image info](https://github.com/Shakun8/BDSecCTF-2022/blob/main/images/13.JPG?raw=true)

well here , it's like damn it is the exploit , so i wasn't wrong .!


# WELL THAT'S WAS ALL , I HOPE LIKE THIS 

# FULL VIDEO OF ALL THIS THINGS : 

i had some problemes regarding time of the video so i didn't intercept all the traffic and stuff : 




[![Click here](https://img.youtube.com/vi/cP5UoxQI-0c/0.jpg)](https://www.youtube.com/watch?v=cP5UoxQI-0c) 


# I HOPE YOU ENJOY IT 

and at end "i recommand to use linux inteand of all this xd"
![image info](https://pbs.twimg.com/media/C_7I8OgUIAALg0r?format=jpg&name=900x900)
