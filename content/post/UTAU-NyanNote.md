---
title: UTAU 调声喵手记
date: 2021-03-21 03:27:12
categories:
- 笔记
tags:
- UTAU
---

本喵在调声时的一些心得技巧，以及一些推荐的工具

>由于 Nyaacinth 自己所使用的系统为 Archlinux，故本文主要面向使用 Linux 发行版与 Wine 的情境作成。

## 本体安装与汉化

请前往 http://utau2008.xrea.jp （国内用户可左转 http://utau.bemanicn.com ）下载 UTAU 安装包，截止本文写成时最新版本为 v0.4.18e，最后更新时间 2013 年 9 月 5 日（Ameya 催更！）

下载后可直接安装，Wine 和 UTAU 都不需修改即可完美运行，这里推荐使用 PlayOnLinux 等工具创建隔离的 Wine 环境

安装过程中连续点击下一步即可，没有需要特殊配置的部分

之后请在 http://utau.bemanicn.com 处下载汉化文件，解压得到的 res 文件夹直接放入 UTAU 安装目录中覆盖同名文件夹即可完成汉化

如果使用 PlayOnLinux 等工具创建了隔离的 Wine 环境此处建议将 `LANG=ja_JP.UTF-8` 添加到在运行程序前执行的命令中防止部分插件的乱码（等效于 Windows 的转区操作）

```
提示：在此之前请在 `/etc/locale.gen` 中反注释 `ja_JP.UTF-8` 一行并执行 `locale-gen` 以添加所需的 Locale</br>
参考： [Locale (简体中文) - ArchWiki](https://wiki.archlinux.org/index.php/Locale_\(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87\))
```

## 选择合成引擎

在安装完成后 UTAU 还不能直接开始使用，咱们需要先选择一个适合的引擎 ~~除非你想无印 Resampler~~

常见的有以下几种：

1. <font color=green>fresamp</font> by *飴屋P*
    - 拉伸式引擎，合成声音偏硬
    - 包含三个版本，分别配布地址
        - [fresamp014omp](https://t.co/DhZsZP29ci)
        - [fresamp014](http://bit.ly/VPQVK7)
        - [fresamp011](http://bit.ly/ae63qb)

1. <font color=green>TIPS</font> by *ScientistB*
    - 拉伸式引擎，适合杂音小、采样长的音源
    - 配布地址
        - [原配](http://scientistb.web.fc2.com/program)
        - [二配（百度网盘）](http://pan.baidu.com/s/1slqEuPR)

1. <font color=green>moresampler</font> by *KanruHua* （推荐）
    - 默认为循环式参量引擎，可设置为拉伸式引擎
    - 可高度自定义，需一定配置才能发挥全部性能，详请参考[这篇](https://www.bilibili.com/read/cv1007842)文章
    - 可能不适合部分气声较重的音源
    - 使用时需将 tool1、tool2 均指定为 moresampler.exe
    - 配布地址
        - [~~原配 1~~](https://web.engr.illinois.edu/~khua5/index.php/moresampler)（原作者毕业，网站已停止运行）
        - [原配 2](http://ch.nicovideo.jp/ejiwarp)

## 音源导入

当然，你还需要一个[音源](https://utauchn.huijiwiki.com/wiki/%E9%9F%B3%E6%BA%90)，你可以直接在搜索引擎中查找关键词 `UTAU音源` `配布` ，也可以查看一下 [UTAU 的萌娘百科页面](https://zh.moegirl.org.cn/zh-cn/UTAU)。这里 Nyaacinth 根据自己的喜好私心推荐 夏语遥 作为新手入门的 UTAU 音源，教程完整使用便捷且质量较高

## 工具推荐

由于 UTAU 的万年不更行为（最后一次更新在 2013 年），其有许多特性或行为与现在的常见引擎/音源不适应，需要添加一些辅助插件来予以修正

### [拡張エンベロープエディタ（UTAU 插件）](http://z-server.game.coocan.jp/utau/utautop.html#pitedit) by *Zteer*

中文一般为扩张 PIT 编辑器，强烈建议安装一个，相对于 UTAU 内置的音高编辑器有诸多优点：

- 音高参照线：原版内置的编辑器是无参考的相对音高绘制模式，基本要靠<ruby>经验<rt>猜</rt></ruby>才能绘制正常的音高，比较接近 Vocaloid V3 的 PIT 参数绘制模式，但实际体验并不好，这个插件增加显示了音高参照线予以补足，方便用户操作

- 多音符联合调整：这个插件可以同时操作选定的多个音符，UTAU 由于数年间从未更新已不适应现在普遍采用的连续音模式，音符首末相接处仍允许存在两个截然不同的音高，反而需要由引擎去推测此处应当采用的唯一音高，是导致相同参数不同引擎的合成效果差异显著原因之一。扩张 PIT 编辑器会对齐两音符首末音高，消除首末相接音高差异以符合连续音一般工作模式

- 直线、曲线工具与缩放功能：原版内置的编辑器另一点不足就是只有直接绘制功能，不支持使用直线与曲线工具，众所周知鼠绘精度难以把握，配合没有音高参照线的白板折线图体验可谓灾难。这个插件同样补齐了短板，好耶

### [presamp（合成/拼接引擎转换层，特殊插件）](http://ch.nicovideo.jp/delta_kimigatame/blomaga/ar591802) by *デルタ*

此插件还有一个国内的配布地址： http://riskucvvc.lofter.com/presamp

使用词典自动将单独音歌词转化为日文连续音、CVVC音源的拆分音符格式并推入引擎合成的特殊插件。由于其直接作为引擎与UTAU转换层的特殊性质，使用时需指定 Tool1、Tool2 为 presamp.exe ，并在其提供的插件 predit 中指定实际调用的引擎

另外此插件需要音源适配，即音源的 oto.ini 同文件夹下需要有相应的 presamp.ini 才能工作，~~否则堪比制作鬼畜音MAD时音频文件七零八落的效果~~

*在使用像 VOICEMITH 等制作公司开发的音源或部分新近连续音音源时本插件可能成为必选项*

### [VocalShifter（独立程序）](http://ackiesound.ifdef.jp/download.html) by *Ackie Sound*

是一个比较强大的工具,一方面可用于对部分质量低下的音源或鬼畜素材进行修平处理或音色修正，另一方面也可以对输出的干声进行部分修正，可以参考[这篇教程](https://www.bilibili.com/read/cv3113948)使用

### [Pitch Monitor（独立程序）](http://ackiesound.ifdef.jp/download.html) by *Ackie Sound*

可以通过麦克风录音来分析音高，适用于不知道对音高参数没有概念但本身有一定歌唱功底或大致有音高参数概念但没有歌唱功底的人类，后者可以通过这个软件了解人类一般歌唱时会出现的转音/长音特征和缺陷，可以学习如何使歌声更加拟合人类歌唱效果。

### [WaveTone（独立程序）](http://ackiesound.ifdef.jp/download.html) by *Ackie Sound*

可以用于扒谱，本身可以检测歌曲的曲速，解析各个乐器的可能音高等（实际上就咱自己而言更多会听几遍学着唱，然后借助 Pitch Monitor 来分析音高扒谱），可能会有人觉得这个工具更好用些

### [UtaFormatix（Web 应用）](https://sdercolin.github.io/utaformatix3) by *sdercolin, ghosrt, shine5402, 時雨ゆん*

用于将不同合成引擎的工程文件转换的 Web 应用，需要使用到一些无参 vsqx/dv/sk 之类的工程文件时可以利用这个应用将它们转换为 ust 工程，同理也可以将 ust 工程转换为其他引擎的工程文件（只是无论哪个方向一般都会丢失除歌词外的几乎所有参数）

Github: https://github.com/sdercolin/utaformatix3 （可本地部署）

### [長い休符を分割（UTAU 插件）](http://ux.getuploader.com/yuuboku/download/47/restslicer.uar) by *遊牧家族*

用途十分明了，是用于将极长的休止符（"R" 音符）分割成较短的多个休止符的小插件，方便预览播放和生成小段示例，实用插件

## 故障排查

### UTAU 本体

- 汉化无效/乱码

    - 如果现在界面为日语
        - 从最上方工具栏打开 “ツール” > “オプション”
        - 在弹出窗户的第二行标签栏中找到 “その他” 并点击进入
        - 找到 “インターフェース言語を強制する” 选中，并在其后的菜单栏中选择 “en”

    - 如果现在界面乱码
        - 重做 “本体安装与汉化” 一章，使用 PlayOnLinux 创建隔离的 Wine 环境并设置 Locale（`LANG=ja_JP.UTF-8`）
        - 重启 UTAU
        - 现在界面应当变为日语，请参考上方的日语界面解决方案操作

- 插件乱码
    - 重做 “本体安装与汉化” 一章，使用 PlayOnLinux 创建隔离的 Wine 环境并设置 Locale（`LANG=ja_JP.UTF-8`）
    - 重启 UTAU
    - 若现在界面变为日语，请参考上方汉化无效部分的日语界面解决方案操作

- 插件无法启动
    - 温馨提示：你在使用 Wine 哦，肯定会有部分应用程序工作不正常的，UTAU 插件也不例外，请尝试各种可能的解决方案或寻找替代品吧（逃

## 推荐访问

- 资源下载-调声相关工具 - https://utaujc.jimdofree.com/download1

- UTAU 部落群（QQ 群组）
