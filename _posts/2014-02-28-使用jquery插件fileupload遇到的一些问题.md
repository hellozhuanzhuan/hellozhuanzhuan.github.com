---
layout: post
title: "使用jquery插件fileupload遇到的一些问题"
description: ""
category: 
tags: []
---
今天想实验一下用fileupload来以ajax的方式上传文件，但是就遇到了一些奇怪的问题，这里先总结一下。

要使用fileupload插件，需要引入如下几个文件
`<script src="http://192.168.1.197/my/js/vendor/jquery.ui.widget.js" type="text/javascript"></script>`

`<script src="http://192.168.1.197/my/js/jquery.iframe-transport.js" type="text/javascript"></script> `

`<script src="http://192.168.1.197/my/js/jquery.fileupload.js" type="text/javascript"></script>`

但是使用的时候就遇到问题了，chrome一直报错说Uncaught TypeError: Object [object Object] has no method 'fileupload'

然后我就纳闷了，在网上搜索一番之后，在github的一个讨论页里找到了答案。<a href="https://github.com/blueimp/jQuery-File-Upload/issues/1085" target="_blank" >链接在此</a>

`Ok, I just figured it out. I simply needed to include jquery.fileupload.js after jquery.ui.widget.js and jquery.iframe-transport.js . At least I know for future reference.`

**没错，上面那三个js文件居然特喵的有引入顺序！！！！！！**

蛋疼的改完之后，果然chrome不再报错了，顺利使用，但是我想要的功能依然没法实现，就是在上传文件的同时，如果有一个文本框需要输入对文件的描述，或者需要同时传输其他数据，用fileupload的方式貌似不行，还是得用form表单提交。
