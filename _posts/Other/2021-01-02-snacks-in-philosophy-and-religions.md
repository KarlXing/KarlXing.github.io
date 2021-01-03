---
layout: post
comments: true
title: "Snacks In Philosophy And Religions"
date: 2021-01-02 00:00:00
tags: buddhism religion
cate: Philosophy-of-Religion
---

> Man is only a reed, the weakest in nature, but he is a thinking reed. 
> 
> -- <cite>Blaise Pascal</cite>

<!--more-->
关于哲学和宗教的一些随想 :)

{: class="table-of-content"}
* TOC
{:toc}
<br>


## Buddhism

### 1. 循环神经网络和六根、六尘、六识

佛学里的六根、六尘、六识粗略上其实挺好理解的，根可以理解为器官或功能，尘是根的输入，识是根的输出，六个一一对应，然后六根和六尘合称十二处，再加上六识又合称十八界。

![六根、六尘、六识]({{ '/assets/images/sixroots.png' | relative_url }})
{: style="width: 60%;" class="center"}
*Fig. 1. 六根、六尘、六识. (Image source: [wiki](https://zh.wikipedia.org/wiki/六根))*
{: style="text-align: center"}

眼耳鼻舌身五根都很直观，但是关于意根就形而上一些了。这也是为什么我们看到一些文章解释根为器官**或功能**，因为没啥器官可以直接对应意根。意根的输出是意识还是好理解的，但是意根的输入被称为法尘，那是什么？意根本身又是什么呢？

首先呢，意根自己以及它的输入和输出都是在精神层面。然后对于上面两个问题
1. 意根的输入（法尘）就是六识，包括意识
2. 意根本身也存在于精神层面，但是和六识当然是不同的，所以佛教称其为第七识（末那识）  

然后有趣的问题就是为什么意根的输入还包括意根的输出呢？因为意根就是一个循环神经网络...上一时刻意根的输出会成为一下时刻意根的输入。其实六根六尘六识用神经网络来解释也挺好的，眼耳鼻舌身五根就是feedforward networks，意根是一个recurrent neural network。对于意根来说，在每个时刻，眼耳鼻舌身五根先对对应的五尘做一个preprocessing，再和意根上一时刻的输出concatenate到一起，一起作为意根的输入，最后输出这一时刻的意识。

<br>
![循环神经网络]({{ '/assets/images/sixroots_nn.png' | relative_url }})
{: style="width: 100%;" class="center"}
*Fig. 1. 用神经网络来理解六根、六尘、六识.*
{: style="text-align: center"}
<br>
