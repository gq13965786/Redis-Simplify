分享来源:https://blog.csdn.net/lishuhuakai/article/details/53321203

# Redis-Simplify
对`Redis v3.0` 的代码做了简化,然后一步一步地往最初的代码里添加新的功能,当然,这一切都是为了方便理解源码而已,总体而言,`Redis`是很漂亮的`c`风格的代码,值得一读.



这个项目从最基本的统一事件源开始,逐步加入各式各样的命令的实现,然后加入序列化的实现,加入事务的实现,最终剥出了了一个单机版本的数据库.



如果你对它的实现原理感兴趣,推荐看<<Redis设计与实现>>这本书:

![Redis设计与实现](http://img.blog.csdn.net/20161124161052469)

# 读源码的建议

读源码,其实是一件非常有讲究的事情,盲目地去读,其实会感觉到特别的累,收获也不一定会特别大,结合我自己读 **redis** 源码的经历,我想在这里给有同样想法的人提一些建议吧.

## 不要死抠细节

这点非常重要,读源码,必须得有不求甚解的能力,我曾经有段时间是这样来读源码的,如果在 **A** 函数的实现中调用了 **B** 函数,我会立马去追踪 **B** 函数干了什么, 如果 **B** 函数的实现中调用了 **C** 函数,我又会立马去追踪 **C** 函数干了什么,这样一层一层地追踪下去,往往到了最后,我就回不来了,不知道我最初到底想干什么.毕竟人脑不是计算机,不能很好地实现递归的功能,所以要不求甚解,在 **A** 函数的实现中,如果出现了 **B** 函数,我们只需要关注 **B** 函数究竟实现了什么功能,将B看做是一个黑盒子,这样的话,会轻松很多.

## 重点关注数据结构和算法以及设计模式,而不是流程

数据结构和算法以及设计模式是程序非常重要的一部分,至少在我看来,懂了这部分东西,基本上,这份源码最漂亮的部分,你已经领会了一半,而另外的一部分,应该是程序的构架.

除了这部分,程序中会有相当部分繁杂的函数调用,以前不懂事的时候,我特别想看看,这个函数调用了那个函数,然后有调用了那个函数,死抠函数的调用关系在当时是非常爽的,因为会有一种 这个程序都在我的掌控之下的错觉, 但是过一段时间之后,你会把这些繁杂的调用关系忘得一干二净, 所以一段时间之后,你的收益是是非常小的. 

"乱花渐欲迷人眼", 请一定要善于抓住程序中最重要的部分,其余的东西最多只是对于这些东西的一个补充,不要陷入茫茫的细节大海中,那些嘈杂的东西,对于我们没有丝毫益处.

## 关注程序的架构

这部分内容对于我们小白和中手来说,还是有一定难度的, 但是对于老手和高手来说,这一部分内容往往才是最重要的东西.程序在老手们看来,不过是架构加上各种数据结构以及算法,其余的东西,不过是为了实现需求而堆砌而成.

比如说,这个 **Redis** 的代码,整体采用了 **Reactor** 的模型在管理各种IO事件, 我从中可以吸取到多少养分(采用这种模型的效率如何?响应时间如何?),将来可以用到自己的代码中,这是个因人而异的事情.

## 有选择的关注程序中的实现

代码中并不是所有部分的内容你都需要了如执掌, 当然也没有必要,但是如果有部分非常重要的部分你不知道的话,你还是要好好关注一番的,看看人家是怎么实现这些功能的.

我这里举一个例子,那就是 **Redis** 是如何实现数据库的AOIC特性以及如何实现持久化的,这些点非常有意思.值得一探究竟.

## 善于利用调试工具

如果有的部分读不懂的话,不妨用 **gdb** 或者 **vs** 来调试一番,看看究竟发生了什么事情, 这里的 **10** 个版本的代码都可以正常编译, 有兴趣的话,不放调试一下.


# 关于各个版本

## 01-Contain-event-loop

这个版本基本上只包含了事件循环,是最为简单,也是最为重要的一个版本.想学习Redis的关于网络部分代码的基本上看这个版本就可以了.后面的代码更多的是关于如何实现一个数据库的.

## 02 - ~~~

在第一个版本的基础上增加了一些代码,不过增加的量不算太大.

## 03 - finished_string_command

在第二个版本的基础上,将 **Redis** 字符操纵的命令这一块实现了.

## 04 - hash_command_finished

在第三个版本的基础上,将 **Redis** 中有关于 **hash** 表这部分命令摘抄出来了.

## 05 - list_command_finished

在第四个版本的基础上,添加了关于 **list** 的命令.

## 06 - set_command_finished

在第五个版本的基础上,添加了关于 **set** 的命令.

## 07 - zset_command_finished

在第六个版本的基础上,添加了关于 **zset** (有序集合)的命令.

## 08 - almost_all_important_commands_finished

在第七个版本的基础上,完善了一些命令,这个版本,已经将这个项目所有要实现的命令全都实现了,这个版本添加的命令主要是关于键过期的.

## 09 - add_file_save

在第八个版本的基础上,添加了 **AOF** 以及 **RDB** 两大文件存储功能.

## 10 - all_is_done

之前的目标基本达成,消除了bug, 增加了事务的功能.


# 日志

## 2016.12.03:

代码现在已经非常臃肿了.看来`C`的代码到了一定的程度之后,真的变得很难管理了.个人突然觉得1到2万的`C`代码还在掌控之内,再多就已经超过我能掌控的范围了.换句话说,你不可能做到细节与宏观都了解得一清二楚,太费劲了.

这或许也是为什么人们要提出抽象的原因吧,我只规定一套接口,我的代码依赖于这套接口之上,至于你怎么实现,那就和我没有多大的关系了.

还有一点,`C`工程写大之后,其实你去看它的代码,其实里面用了很多面向对象的思想,包括数据的封装,代码的复用,感觉越写越像`CPP`.代码我已经剥了很大的一部分了,应该用不了多久就能剥出一个单机`NoSql`数据库了.然后准备开始写`Redis`的客户端了.



## 2016.12.04:


修复了一些`bug`,然后下一步的工作是可以将内存中的数据写入文件,并且启动时可以导入文件,这样的话,一个单机版的数据库基本完工.



## 2016.12.10:

时间过得真快,最近忙到都没有时间来打理这个东西了,好了,我想说的是,这个版本实现了数据存储到磁盘的功能,`aof`以及数据库的保存都已经补完了,这个项目也接近尾声了,下一个版本,我想加入事务的功能,貌似挺有意思的.



## 2016.12.10:

好吧,今天加了一点班,将单机数据库所需要的全部东西都剥离了出来,好吧,其实就是添加了事务这样一个功能,有了事务,`Redis`才能被称作数据库.这就是说,这个项目差不多就到头了,将来可能会改一点东西,但是不会很大了,多机数据库的部分我就不添加了,再弄下去,就和源码没多大区别了,(当然,我也剥不下去了,因为代码量太大了点)所以其余的,你如果感兴趣的话,自己去看源码的,我的源码的版本是`3.0`.


## 2017.08.07:

距离上一次的代码修改,已经将近过去大半年了,事实上,上几次做的还不够彻底,因为,我并没有书写完整的 `makefile` 文件,所以,这一次,估计也是最后一次,我会将这几个版本的代码完善一下,并且将 `makefile` 文件写完.



## 2017.08.12

说实话, **Redis** 的源码是在太大了,看起太费劲了,还好,今天终于结束了.






完结撒花.用了大半个月来剥离源码,说实话,源码还是很有意思的,能学到多少东西,这是个仁者见仁智者见智的问题啦.对我来说,不亏,好像也不赚,就这样吧,下一步就是用`cpp`写一个`Redis`客户端了,相比于服务端,客户端的实现要简单太多太多.
