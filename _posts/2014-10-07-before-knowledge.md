---
layout: post
title: "发送HTTP和HTTPS报文--预备知识"
categories:
- java
---

&emsp;&emsp;写一些小爬虫，给Http协议打交道是难免的。抓取别人的网页说白了，也就是模拟发送http的报文，然后对得到的结果处理。这里简单介绍几个必须了解的概念，关于协议之类的细节，你可以自行学习。这里推荐只几本书和一些学习资料。

教学视频（自己学院录得视频感觉比较好）<br/>
[http://sharecourse.upln.cn/pdt/sharecourse/modules/mod_course/app/course.php?cid=47c6381b-3aae-4cf6-a4f9-1cf6d28bf179](http://sharecourse.upln.cn/pdt/sharecourse/modules/mod_course/app/course.php?cid=47c6381b-3aae-4cf6-a4f9-1cf6d28bf179)<br/>
推荐书籍<br/>
《计算机网络（第六版）》 谢希仁 编著 &emsp;&emsp;&emsp;[点击下载PDF和课件](http://pan.baidu.com/s/1bnhGF3T)&emsp;密码：e7u5<br/>
>这本书是我接触网络的入门书。书写的比较容易易懂，如果你没有接触过网络，就从这本书开始吧。

《TCP-IP详解卷一：协议》&emsp;&emsp;&emsp;&emsp;[下载PDF](http://pan.baidu.com/s/1bnaVd63)&emsp;密码：fb4v<br/>
>这本书写的比较早，现在又很多标准已经改变了。“尽信书，不如无书”，学会在看书的时候思考。

《HTTP权威指南》&emsp;&emsp;&emsp;&emsp;[下载PDF](http://pan.baidu.com/s/1c06bk7Q)&emsp;密码：0f1i
>这本书，我看了不少于3遍。一边看书，一边做笔记，一边思考。这本书细致讲解了HTTP协议的细节，我觉得只要你做网站开发（无论何种语言，无论前端后还是后台），细读此书，你会有不一样的收获。

**&emsp;&emsp;虽然，网络是开放的，随时可以下到书籍资料。但是，我觉得还是应该买本书看看。那样，才有感觉。不缺这几个钱，对吗？**

#### 说几个概念 ####

1. HTTP协议是基于**字符**的。<br/>
&emsp;&emsp;**TCP/IP协议中，数据链路层是基于字节的，网络层是基于报文的**。首先，“基于”这个词，应该揣摩一下。意思就是，站在人的角度能分析理解的意思。也就是，数据链路层，我们只能看到些01的代码。网络层，可以看到报文和报文的组织结构。HTTP协议，我们能看到字符（例如 get /index.html http1.0）这些字符，都是我们能够理解的。这一点，如果理解还是不够透彻的话，试着用wireshark抓包分析一下，一切都明了了。<br/>
&emsp;&emsp;HTTP协议基于字符的设计，是设计为了程序开发人员故意设计成字符可见的。意在，方便开发者应用，降低学习成本（这一点还是比较赞的）。
2. 连接控制

3. 会话控制

4. 