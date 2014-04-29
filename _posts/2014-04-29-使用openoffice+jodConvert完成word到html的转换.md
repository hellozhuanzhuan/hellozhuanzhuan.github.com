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
package com.mzule.doc2html.util;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.ConnectException;
import java.util.Date;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import com.artofsolving.jodconverter.DocumentConverter;
import com.artofsolving.jodconverter.openoffice.connection.OpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.connection.SocketOpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.converter.OpenOfficeDocumentConverter;

				/**
				 * 将Word文档转换成html字符串的工具类
				 * 
				 * @author MZULE
				 * 
				 */
				public class Doc2Html {
				
				    public static void main(String[] args) {
				    System.out
				        .println(toHtmlString(new File("C:/test/test.doc"), "C:/test"));
				    }
				
				    /**
				     * 将word文档转换成html文档
				     * 
				     * @param docFile
				     *                需要转换的word文档
				     * @param filepath
				     *                转换之后html的存放路径
				     * @return 转换之后的html文件
				     */
				    public static File convert(File docFile, String filepath) {
				    // 创建保存html的文件
				    File htmlFile = new File(filepath + "/" + new Date().getTime()
				        + ".html");
				    // 创建Openoffice连接
				    OpenOfficeConnection con = new SocketOpenOfficeConnection(8100);
				    try {
				        // 连接
				        con.connect();
				    } catch (ConnectException e) {
				        System.out.println("获取OpenOffice连接失败...");
				        e.printStackTrace();
				    }
				    // 创建转换器
				    DocumentConverter converter = new OpenOfficeDocumentConverter(con);
				    // 转换文档问html
				    converter.convert(docFile, htmlFile);
				    // 关闭openoffice连接
				    con.disconnect();
				    return htmlFile;
				    }
				
				    /**
				     * 将word转换成html文件，并且获取html文件代码。
				     * 
				     * @param docFile
				     *                需要转换的文档
				     * @param filepath
				     *                文档中图片的保存位置
				     * @return 转换成功的html代码
				     */
				    public static String toHtmlString(File docFile, String filepath) {
				    // 转换word文档
				    File htmlFile = convert(docFile, filepath);
				    // 获取html文件流
				    StringBuffer htmlSb = new StringBuffer();
				    try {
				        BufferedReader br = new BufferedReader(new InputStreamReader(
				            new FileInputStream(htmlFile)));
				        while (br.ready()) {
				        htmlSb.append(br.readLine());
				        }
				        br.close();
				        // 删除临时文件
				        htmlFile.delete();
				    } catch (FileNotFoundException e) {
				        e.printStackTrace();
				    } catch (IOException e) {
				        e.printStackTrace();
				    }
				    // HTML文件字符串
				    String htmlStr = htmlSb.toString();
				    // 返回经过清洁的html文本
				    return clearFormat(htmlStr, filepath);
				    }
				
				    /**
				     * 清除一些不需要的html标记
				     * 
				     * @param htmlStr
				     *                带有复杂html标记的html语句
				     * @return 去除了不需要html标记的语句
				     */
				    protected static String clearFormat(String htmlStr, String docImgPath) {
				    // 获取body内容的正则
				    String bodyReg = "<BODY .*</BODY>";
				    Pattern bodyPattern = Pattern.compile(bodyReg);
				    Matcher bodyMatcher = bodyPattern.matcher(htmlStr);
				    if (bodyMatcher.find()) {
				        // 获取BODY内容，并转化BODY标签为DIV
				        htmlStr = bodyMatcher.group().replaceFirst("<BODY", "<DIV")
				            .replaceAll("</BODY>", "</DIV>");
				    }
				    // 调整图片地址
				    htmlStr = htmlStr.replaceAll("<IMG SRC=\"", "<IMG SRC=\"" + docImgPath
				        + "/");
				    // 把<P></P>转换成</div></div>保留样式
				    // content = content.replaceAll("(<P)([^>]*>.*?)(<\\/P>)",
				    // "<div$2</div>");
				    // 把<P></P>转换成</div></div>并删除样式
				    htmlStr = htmlStr.replaceAll("(<P)([^>]*)(>.*?)(<\\/P>)", "<p$3</p>");
				    // 删除不需要的标签
				    htmlStr = htmlStr
				        .replaceAll(
				            "<[/]?(font|FONT|span|SPAN|xml|XML|del|DEL|ins|INS|meta|META|[ovwxpOVWXP]:\\w+)[^>]*?>",
				            "");
				    // 删除不需要的属性
				    htmlStr = htmlStr
				        .replaceAll(
				            "<([^>]*)(?:lang|LANG|class|CLASS|style|STYLE|size|SIZE|face|FACE|[ovwxpOVWXP]:\\w+)=(?:'[^']*'|\"\"[^\"\"]*\"\"|[^>]+)([^>]*)>",
				            "<$1$2>");
				    return htmlStr;
				    }
				
				}

类组织的不好，博友凑合看，代码注释比较详细了，不多说。

两个公开的方法是独立使用的，toHtmlString(...)方法是转化文件并获取html代码，以备存入数据库。

参考了http://dangry.iteye.com/blog/858787，表示感谢。

