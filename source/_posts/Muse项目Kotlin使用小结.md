---
title: Muse项目Kotlin使用小结
date: 2016-06-30 11:02:44
tags: [Kotlin, Android]
description: "总结Muse项目中使用Kotlin带来的便利"
---
# 起源
4月中旬来到新公司，遇到新项目Muse的启动，由于没有历史包袱，技术用的比较激进，除了部分公用库之外，整个项目都基于Kotlin完成。历时1个半月，项目成功上线，整个过程中Kotlin带来了诸多便利，这里介绍下配置方法，并总结下用Kotlin替代Java带来的便利。供后续翻阅。
>注意：本文不是Kotlin教程，只是总结了一些Kotlin提供的便利

# 配置
由于Kotlin是JetBrains推出的，Android Studio对其的支持度很高。在普通Android项目的基础上增加Kotlin配置只需要在插件管理中安装Kotlin插件并执行`Tools - Kotlin -Configure Kotlin in Project`即可
![安装Kotlin插件](http://7xored.com1.z0.glb.clouddn.com/blog_kotlin_plguin.png?imageView2/2/w/600/q/100)
![配置Kotlin](http://7xored.com1.z0.glb.clouddn.com/blog_kotlin_configure.png?imageView2/2/w/600/q/100)
# Kotlin的便利性
首先最大的便利是语句的结尾不用写分号。刚开始使用时觉得没啥，就是写一个分号的事情，但是用久了回头再写Java的时候，不写分号出现编译错误的时候才会怀念Kotlin。
## 扩展方法
之前项目过程中经常出现各类Utils类，比如StringUtils、DateUtils等，这种情况下刚入手项目的同事可能不清楚该方法的存在，导致同一功能的重复实现。
而在Kotlin中，可以为已有类拓展方法，结合IDE的代码提示功能，方便找到同事已定义的类拓展方法，避免代码冗余。
举个例子：在商城类项目中经常会出现需要显示金额“￥”的情况，并且金额的数字要保留指定位数，并且有四舍五入等规则。那么，在Java中，我们通常会定义一个Util类，然后里面会有一个format方法。而使用Kotlin，我们可以直接对Double、Float、Int等数值类型进行拓展，以Double为例，代码如下
```
fun Double.Yuan(): String{
    val formatter = DecimalFormat("0.00")
    formatter.roundingMode = RoundingMode.DOWN
    return "￥"+formatter.format(this)
}
```
定义该方法后，输入Double类型变量以及“.”之后，IDE会提示此方法的存在。这样我们就不需要再去Utils类中翻查方法。
![拓展方法提示](http://7xored.com1.z0.glb.clouddn.com/blog_kotlin_ext.png)
## Null Safety
过去项目上线后，Crash中至少有一半是`NullPointerException`，相信大部分Andorid或者Java程序员对此都深恶痛绝。Kotlin从源头上就避免这种情况的发生，它将可用为空的引用和不可为空的引用进行区分，我们不能将`null`赋值给一个不能为空的引用，而可为空的引用在使用时必须手动对其进行显示的判断或者使用`?.`的方式进行安全的调用。
通过这种方式，Crash率大幅降低，目前Crash中几乎没有出现过空指针的异常。
## Anko
Android的UI通常是使用xml文件进行布局，虽然可以使用Java代码进行创建，但是写起来较为复杂，Kotlin的拓展库Anko提供了一种极为方便的方式供我们使用Kotlin代码进行界面的构建。
```
verticalLayout {
    padding = dip(30)
    editText {
        hint = "Name"
        textSize = 24f
    }
    editText {
        hint = "Password"
        textSize = 24f
    }
    button("Login") {
        textSize = 26f
    }
}
```
例如上面的代码创建了带有30dp的横向的布局，内含两个EditText和一个Button组件。可以想象一下这样的简单界面使用Java代码构建的工作量。通过Anko，当需要简单的界面比如列表Adapter内的布局时，可以无需创建layout文件，提高效率。
## 常量
Kotlin中的常量和Java中的final修饰的变量类似。在Kotlin中，推荐使用常量，仅当确定值会变化的情况下才使用变量，这样可以提高运行效率并避免在使用过程中引用在某个不起眼的角落被更改，从而出现奇怪的问题。
## Lazy-init
我们经常会遇到Activity或者Fragment中获取intent中传递过来的参数，通常做法是在类开头处进行声明，然后在onCreate等方法中进行。
但是这样带来的问题是当我们需要知道这个变量的来源时，我们需要翻到初始化的地方，不太方便。在Kotlin中通过懒加载的方式，可以很好的解决这个问题。如：
```
    val mRoomId by lazy { intent.getLongExtra(EXTRA_CHATROOM_ID, 0) }
```
这样，在变量声明的地方，我们就能比较明显看到它的初始化方式。
## Data类
每个客户端项目都会有一系列的称为Model或者Bean的类，用来表示我们的数据模型。通常我们需要为这些类添加Getter/Setter、equals/hashCode、toString等方法。虽然IDE可以帮助我们生成，但是这样的重复劳动确实不被讨喜。
在Kotlin中，我们可以使用`data `关键字来修饰某个类，就可以省下上述这些方法。
例如，声明一个User类：
```
data class User(val name: String, val age: Int)
```
## Lambdas
Lambda的使用已经出现在Java8中，但是由于Android当前仍然使用旧版本的Java运行环境，并不支持Lambda表达式，虽然有第三方的库可以提供帮助，但毕竟不是官方的方式。而Kotlin原生变支持lambda，给编程带来极大便利性。
还是使用常见的例子来说明，`View.setOnClickListener()`这个方法所有的Android程序员都使用过。通常，我们会这么写：
```java:n
view.setOnClickListener(new OnClickListener(){
    @Override
    public void onClick(View v) {
            ...
    }
})
```
然后，翻译成Kotlin
```
view.setOnClickListener(object : OnClickListener {
    override fun onClick(v: View) {
    ...
    }
}
```
嗯，基本没啥变化，仅仅是语言上的差异
然后我们使用Lambads的形式，箭头左边定义参数，右边是逻辑处理和返回值
```
view.setOnClickListener({ view -> ...})
```
简化很多。继续，如果参数没有被使用到的话，就可以省略参数，于是变成这样：
```
view.setOnClickListener({ ...})
```
最后，如果方法中最后一个参数是一个函数，并且只有一个元素，可以再化简
```
view.setOnClickListener{ ...}
```
嗯，5行代码的事情一行就能解决，打打提高了效率。
# 总结
Kotlin带来了一种回不去的感觉，再次用Java去写Android程序的时候会有怎么这么啰嗦的感觉。不过Kotlin目前才1.0.2，未来不知道是否会成为主流，在成熟的大项目中使用依然有风险，利弊需要团队自己来权衡。

