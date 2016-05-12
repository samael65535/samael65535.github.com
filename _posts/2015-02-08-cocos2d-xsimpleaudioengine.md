---
layout: post
title: "改造Cocos2d-x中的SimpleAudioEngine"
categories: cocos2d
---

# 背景 #

最近在制作一个音乐类的游戏, 很关键的一环就是需要谱表与音乐播放的时间贴合. 为了能得到游戏音乐的播放时间,按着能尽量不动引擎源码的方法, 比较直接有两种办法:

* 使用第三方库的游戏音效库, 如 [FMOD]

* 与此同时在Cocos2d-x 3.3的版本更新中也发提供了基于OpenAL的音频接口

{% highlight C++ %}
	cocos2d::experimental::AudioEngine
{% endhighlight %}

# 分析 #

这两种方法都有各自优缺点:

## [FMOD] ##

优点:

1. 功能强大, 如支持3D音乐音效

2. 并不算困难的移植, 在CocoaChina2014秋季开发者大会中的[Game audio made easy with FMOD Studio]演讲中说了对Cocos2d-x的支持

缺点:

1. 功能强大的反面就是学习成本的增加,而且游戏的在Cocos2d-js的框架下开发的, 所以还样做一层JSB, 所以为了两个API去实现这么多的集成成本有些大.

2. 策划的学习成本的增加.

3. 制作商业游戏的授权费用.

## AudioEngine ##

在Cocos2d-x的版本更新中新加出的AudioEngine的类, 基于OpenAL进行实现, 在应该类中有实现了获取音乐时间和跳到某一时间的API.
优点就不说了, 只说不足:

1. 虽然有了这样的API但是JSB里没有Binding, 需要自己手动添加.

2. 精度不够, 使用getCurrentTime()时间精度只能达到0.1秒, 不足以支持节奏游戏的精度.(虽然可以调整精度, 但是经常会产生闪退的问题)

3. 有Bug, 这个Bug在官方示例中也有, 就是当在播放音乐的时候接到了电话, 音乐永远不会再播放了, 除非杀掉程序.

4. 莫名其妙的音乐实例会消失

# 解决办法 #

好吧, 只有改SimpleAudioEngine了, 经过实验, 音乐的时间精度可以达到需要的要求, 而且jsb也是没问题的

[SimpleAudioEngine参考资料]

这个资料比较详细的介绍了SimpleAudioEngine的改造, 但是由于时间版本的问题这里的代码还是和实际工程中有些出入.所以还得修改一下(只针对IOS, Android可以自行扩展)

* SimpleAudioEngine.mm

{% highlight objective-c %}
static void static_setBackgroundMusicCurrentTime(float currentTime)
{
    [[SimpleAudioEngine sharedEngine] setBackgroundMusicCurrentTime:currentTime];
}

static float static_getBackgroundMusicCurrentTime()
{
    return [[SimpleAudioEngine sharedEngine] getBackgroundMusicCurrentTime];
}

float SimpleAudioEngine::getBackgroundMusicCurrentTime() {
    return static_getBackgroundMusicCurrentTime();
}
void SimpleAudioEngine::setBackgroundMusicCurrentTime(float currentTime) {
    static_setBackgroundMusicCurrentTime(currentTime);
}
{% endhighlight %}

* SimpleAudioEngine_objc.m

{% highlight objective-c %}
#pragma mark SimpleAudioEngine - setBackgroundMusicCurrentTime
-(void) setBackgroundMusicCurrentTime:(float) currentTime {
    [am setBackgroundMusicCurrentTime:currentTime];
}

-(float) getBackgroundMusicCurrentTime{
    return [am getBackgroundMusicCurrentTime];
}

{% endhighlight %}

* CDAudioManager.m

{% highlight objective-c %}

-(void) setBackgroundMusicCurrentTime:(float) currentTime {
    [self.backgroundMusic.audioSourcePlayer setCurrentTime:currentTime];
}

-(float) getBackgroundMusicCurrentTime{
    return self.backgroundMusic.audioSourcePlayer.currentTime;
}
{% endhighlight %}


[FMOD]:<http://www.fmod.org/>
[Game audio made easy with FMOD Studio]:<http://upyun.cocimg.com/cocos2014/Mathew%20Block-Cocos%20Presentation_Mathew(FMOD).pdf>
[SimpleAudioEngine参考资料]:<http://www.slideshare.net/syuhari/simpleaudioengine>
