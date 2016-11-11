---
category: Android,Gradle
title: Gradle在Android进行编译依赖
layout: post
author: samael
---
最近做的游戏项目需要打各种的渠道包, 对于使用联通计费的渠道包来说, 不同渠道有不同的计费文件需要使用.

在Gradle的插件中有个功能是可以根据productFlavors或者buildType的参数, 自动生成一个compile命令

如:
{% highlight gradle %}
productFlavors {
	xiaomi{}
}

dependencies {
	xiaomiCompile fileTree(dir: 'unicom_payfile/xiaomi, include: '*.jar')
}
{% endhighlight %}

意思就是对于xiaomi的这个flavor使用unicom_payfile/xiaomi下的jar计费文件进行编译

但是对于几十个渠道来说一个一个写显然不是个办法, 秉着程序员应该懒惰的原则, 看了看groovy的一些语言特性, 发现可以写成这个样子:

{% highlight gradle %}
dependencies {
	android.productFlavors.all {
		favor ->
			"${name}Compile"(fileTree(dir: "unicom_payfile/${name}", include: '*.jar'))
	}
}
{% endhighlight %}
