---
layout: post
title: "使用openoffice+jodConvert完成word到html的转换"
description: ""
category: 
tags: []
---
最近一直在想如何把word转换成html的问题，在搜索了一番之后，发现有一种方法实现起来还算可以，就是用[openoffice](https://www.openoffice.org/zh-cn/)+[jodConvert](http://www.artofsolving.com/opensource/jodconverter)的形式进行实现

具体参考了这两篇文章[借用OpenOffice将上传的Word文档转换成Html格式](http://www.cnblogs.com/codeplus/archive/2011/10/22/2220952.html)和[Java实现在线预览office](http://aijava.cn/1939.html)

以后用到的时候就从这里找了哈哈

为了防止文章失效，粘贴一份保存在这里

为什么会想起来将上传的word文档转换成html格式呢？设想，如果一个系统需要发布在页面的文章都是来自word文档，一般会执行下面的流程：使用word打开文档，Ctrl+A，进入发布文章页面，Ctrl+V。看起来也不麻烦，但是，如果文档中包含大量图片呢？尴尬的事是图片都需要重新上传吧？

如果可以将已经编写好的word文档上传到服务器就可以在相应页面进行展示，将会是一件非常惬意的事情，最起码信息发布人员会很开心。程序员可能就不会这么想了，囧。

将Word转Html的原理是这样的：

1、客户上传Word文档到服务器

2、服务器调用OpenOffice程序打开上传的Word文档

3、OpenOffice将Word文档另存为Html格式

4、Over

至此可见，这要求服务器端安装OpenOffice软件，其实也可以是MS Office，不过OpenOffice的优势是跨平台，你懂的。恩，说明一下，本文的测试基于 MS Win7 Ultimate X64 系统。

下面就是规规矩矩的实现。

1、下载OpenOffice，[http://download.openoffice.org/index.html](http://download.openoffice.org/index.html) So easy...

2、下载Jodconverter [http://www.artofsolving.com/opensource/jodconverter](http://www.artofsolving.com/opensource/jodconverter) 这是一个开启OpenOffice进行格式转化的第三方jar包。

3、泡杯热茶，等待下载。

4、安装OpenOffice，安装结束后，调用cmd，启动OpenOffice的一项服务：C:\Program Files (x86)\OpenOffice.org 3\program>soffice -headless -accept="socket,port=8100;urp;"

5、打开eclipse

6、喝杯热茶，等待eclipse打开。

7、新建eclipse项目，导入Jodconverter/lib 下得jar包。

8、Coding...

代码如下


类组织的不好，博友凑合看，代码注释比较详细了，不多说。

两个公开的方法是独立使用的，toHtmlString(...)方法是转化文件并获取html代码，以备存入数据库。

参考了http://dangry.iteye.com/blog/858787，表示感谢。

