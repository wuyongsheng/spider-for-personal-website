# spider-for-personal-website

> 从去年年初开始，就一直在参与公司爬虫大数据的项目，爬取的是各个工商信用网站的数据，我主要负责数据的验证以及部分网站数据的解析工作。最近突然想把自己的个人网站上所有的文章都爬取下来，于是就有了这篇文章。

源代码已上传至GitHub：
https://github.com/wuyongsheng/spider-for-personal-website/blob/master/mySpider

### 思路及分析

想爬取网站上所有的文章，首先得获取每一篇文章的 url ，然后通过 url 发起 http 请求，接着对响应数据的 html 页面源码使用python的爬虫工具库进行解析，从中提取出文章的相关信息。

So，在编写爬虫程序之前，我们必须熟悉相关的爬虫工具库，同时对目标网站的结构也应该有所了解。

### 使用的技术

这里使用 python + BeautifulSoup4（网页装载与解析） + urllib（发起请求）+ 字符串处理函数（字符替换及去掉字符串中的空白字符等） + codecs（写入文件）+ MongoDB （写入 MongoDB）+ urlretrieve（下载网页上的图片到本地）

### 网站及页面结构

-  网站首页

![网站首页.png](https://upload-images.jianshu.io/upload_images/12273007-6a5ffffba40916ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图中，我们可以看到网站的首页的 url 是：http://wysh.site/ ，点击下方分页栏中的页码可跳转至指定的分页

-  网站总页面数
按下键盘的 F12 键，可以看到网页标签的 DOM 结构，下图红框中的标签显示了分页的总数

![总页面数png.png](https://upload-images.jianshu.io/upload_images/12273007-49712cc9cbdb9b30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 其他分页

![其他分页.png](https://upload-images.jianshu.io/upload_images/12273007-84bf663ca315c15f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图中可以看出，除首页以外，其他分页的 url 是：
http://wysh.site/page/page_num/  ,其中 page_num 代表分页的数字

- 首页及分页中文章的 url

![文章标签.png](https://upload-images.jianshu.io/upload_images/12273007-68bba5d6c3f277f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图中，我们可以看出，首页及每个分页都对应着该分页的 5 篇文章标签，每个文章标签里面都有该文章的 url 属性

-  文章内容标签

![文章内容部分.png](https://upload-images.jianshu.io/upload_images/12273007-c5e2a3fa323c7679.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图显示了文章内容的标签，其中文章的正文标签都是 class 为 post-body han-init-context 的直接子节点

###  代码逻辑

进行了上述网站及页面结构的分析，接下来就可以写爬虫代码了。

代码逻辑如下：

1.  请求首页，从响应信息中提取出分页的总数，从而可以推断出各分页的 url
2.  请求首页及个分页的 url，从响应信息中提取出首页及各分页中的文章的 url，加入到列表中，完成之后，可以得到包含所有文章的 url 的列表
3.  使用文章列表中的每一篇文章的 url 发起 http 请求，对请求的响应信息的 html 页面源码进行解析，提取出文章信息，将其写入文件及 MongoDB 中（同时将文章中的图片保存至本地）
4.  针对请求过程中可能出现的异常（比如网络不稳定导致请求识别）进行失败重试（键代码中的 post-body han-init-context 方法）

###  爬虫程序执行后的结果

![文章及图片.png](https://upload-images.jianshu.io/upload_images/12273007-edaf7c91782fdaec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![文章内容.png](https://upload-images.jianshu.io/upload_images/12273007-a3b5bb8d3968752b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![存入MongoDB中的数据.png](https://upload-images.jianshu.io/upload_images/12273007-4846e0188177e328.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

