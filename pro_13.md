# 目录 {ignore=true}
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [环境与工具](#环境与工具)
* [校色后数据](#校色后数据)
* [结论](#结论)
	* [1. sRGB 色域覆盖 97.7% 容积有溢出。](#1-srgb-色域覆盖-977-容积有溢出)
	* [2.  AdobeRGB　色域覆盖81%](#2-adobergb-色域覆盖81)
	* [3. 偏色](#3-偏色)
	* [4. 实际效果](#4-实际效果)
	* [5. 其他](#5-其他)
		* [优点](#优点)
		* [问题](#问题)
	* [6. 建议](#6-建议)
	* [4. 实际效果](#4-实际效果)
<!-- /code_chunk_output -->


# 不负责任的 wacom cintiq pro 13 测评 {ignore=true}

##  环境与工具

测试环境 `windows_10_enterprise_version_1703`
校色工具 `Spyder4`
校色软件 `DisplayCAl 3.3.2`
数位板驱动 `Windows - Driver 6.3.23-1`
显示设备 `Dell U2312HM` *2 `wacom cintiq pro 13`

-----

## 校色后数据

显示器 1 号
![snipaste_20170801_152935](https://i.loli.net/2017/08/01/59802f0269cfe.png)
<!-- ![snipaste_20170801_152950](/assets/snipaste_20170801_152950.png) -->

---

显示器 2 号
![snipaste_20170801_152950](https://i.loli.net/2017/08/01/59802f0cafa00.png)
<!-- ![snipaste_20170801_152935](/assets/snipaste_20170801_152935.png) -->

-----

 Wacom cintiq pro 13
 <!-- ![snipaste_20170801_153357](/assets/snipaste_20170801_153357.png) -->
 ![snipaste_20170801_153357](https://i.loli.net/2017/08/01/59802fda5f34f.png)

校色过程太无聊，直接说结果吧

## 结论

测量结果因环境不同，可能与官方数据不同，此数据仅供参考。

### 1. sRGB 色域覆盖 97.7% 容积有溢出。

    下图为更加详细的色域分布情况。 虚线为标准sRGB范围。彩色为实际测量值。

    可以看出蓝色部分有缺失，绿色部分有溢出。

<!-- ![snipaste_20170801_152051](/assets/snipaste_20170801_152051.png) -->
![snipaste_20170801_152051](https://i.loli.net/2017/08/01/5980375e3837f.png)

**重点：`主要表现为高对比度情况下，部分画面细节消失。`**

### 2.  AdobeRGB　色域覆盖81%  
基本说明同上　
<!-- ![snipaste_20170801_152034](/assets/snipaste_20170801_152034.png) -->
![snipaste_20170801_152034](https://i.loli.net/2017/08/01/5980376e8732e.png)

低于官方标准87% 的原因分析，可能为使用的AdobeRGB标准年代不同。

**重点：默认情况下pro 13使用AdobeRGB色域显示，对sRGB基本上全覆盖，放心的画吧。在其他显示器上色彩都可以正常表现，超过范围的细节会丢失。一般显示器参考上放Dell。**

### 3. 偏色

<!-- ![snipaste_20170801_163101](/assets/snipaste_20170801_163101.png) -->
![snipaste_20170801_163101](https://i.loli.net/2017/08/01/59803d399f503.png)
<!-- ![snipaste_20170801_163142](/assets/snipaste_20170801_163142.png) -->
![snipaste_20170801_163142](https://i.loli.net/2017/08/01/59803d6786330.png)

说明： 在白色背景下，显示颜色会偏蓝。在黑色背景下显示颜色基本正常。
**重点：画完的会偏冷色一点，注意调整**

### 4. 实际效果

下方图片来自网络收集，如有侵权请联络。

对比图片为载入校色文件后显示效果。

左侧为 pro 13 右侧为 Dell

效果不评论

![snipaste_20170801_164937](https://i.loli.net/2017/08/01/598042b50d3b9.png)
![snipaste_20170801_164551](https://ooo.0o0.ooo/2017/08/01/598042e62c526.png)
![snipaste_20170801_164530](https://i.loli.net/2017/08/01/598042ee37ba7.png)
![Untitled-12](https://i.loli.net/2017/08/01/598042f8b0644.png)
![Untitled-1233](https://i.loli.net/2017/08/01/5980430821d26.png)
![Untitled-123](https://i.loli.net/2017/08/01/598043131ae96.png)
![Untitled-1](https://i.loli.net/2017/08/01/5980431b9ed35.png)
![Untitled-11](https://ooo.0o0.ooo/2017/08/01/59804324c623e.png)
<!-- ![snipaste_20170801_164530](/assets/snipaste_20170801_164530.png) -->
<!-- ![snipaste_20170801_164551](/assets/snipaste_20170801_164551.png) -->
<!-- ![snipaste_20170801_164937](/assets/snipaste_20170801_164937.png) -->
<!-- ![Untitled-1](/assets/Untitled-1.png)
<!-- ![Untitled-11](/assets/Untitled-11.png) -->
<!-- ![Untitled-12](/assets/Untitled-12.png) -->
<!-- ![Untitled-123](/assets/Untitled-123.png) -->
<!-- ![Untitled-1233](/assets/Untitled-1233.png) -->

**重点：pro 13 暗部细节更明显，亮部略同。**

如下想知道裸眼效果，需要相机与色卡，有此二物者，也就不必看这个啦。

### 5. 其他

#### 优点
 1. 笔的精确度提高了，贴完膜与纸上绘画相似
 2. 色彩准备
 3. 3个USB-C 随意插，不用担心插错，线有点多。
 4. 比surface pro 好用太多。

#### 问题
 1.  颜色设置与色彩空间设定会失效
![snipaste_20170801_170512](https://i.loli.net/2017/08/01/59804568d622c.png)
 2. 不能手动调整RGB
 3. 触摸只对主显示器有效，多显示无解。到手时差点以为坏掉了。
 4. 关掉 pro 13 的显示后可以当普通数位板用，而且多显示有效，不能理解。
 5. 笔校准需要多次尝试。

### 6. 建议

想入手的，如果是旧电脑，pro 13就够用。pro 16更好，不过要确认电脑是否支持足够的分辨率。预计pro 16性能优于 pro 13

观望的，可以等等，2018年1月有pro 24 与 pro 32 上市。

以上。

<!-- ![微信图片_20170801173212](/assets/微信图片_20170801173212.png) -->

![微信图片_20170801173212](https://i.loli.net/2017/08/01/59804be6b0911.png)
