---
layout: post
title: "wavesurfer的笔记"
---

# 简介 #

wavesurfer是一个使用canvas绘制波形图和操作音频的js库, 并且有一定的扩展能力. 开发者可以方便地进行拓展.

# 使用 #

* 下载了wavesurfer之后会有如下结构:

<img style="margin-left:0" src="/img/wavesurfer_directory.png"/>

* wavesurfer会在网页中搜索设置好的容器名的div并在其中新建一个canvas画布进行波形图的绘制

```html
<div id="waveform">
      <!-- Here be the waveform -->
</div>
```

* 使用Object.create(WaveSurfer)创建一个对象实例

```javascript
var wavesurfer = Object.create(WaveSurfer);
```

* 在document中增一下个DOMContentLoaded事件响应用于初始化波形绘制的一些参数
options里会有更多的选项, 可以到wavesurfer.js中查看默认的选项

```javascript
var options = {
  //表示在html中要绘制波形的容器,在index.html中可以找到
      container     : document.querySelector('#waveform'), // 选择页面中的容器
      waveColor     : 'violet',
      progressColor : 'purple',
      loaderColor   : 'purple',
      cursorColor   : 'navy'
  };
  if (location.search.match('scroll')) {
      options.minPxPerSec = 100;
      options.scrollParent = true;
  }
  if (location.search.match('normalize')) {
      options.normalize = true;
  }
  // Init
  wavesurfer.init(options);
  // Load audio from URL
  wavesurfer.load('example/media/demo.wav');
  // Regions
  if (wavesurfer.enableDragSelection) {
      wavesurfer.enableDragSelection({
          color: 'rgba(0, 255, 0, 0.1)'
      });
```

* 注册事件
wavesurfer中预置了一些事件, 可以对音频进行操作, 如:
``` javascript
wavesurfer.on('ready', function () {
    wavesurfer.play();
});
```
具体可以网站或example中查看

# 插件 #
在wavesurfer里有七个实用插件

* Regions plugin
在音频中设置循环区域

* ELAN plugin
歌词与音频进行同步

* Timeline plugin
在小型上打上时间标记

* Microphone plugin
话筒输入的可视化

* Panner
控制左右声道的插件

* Equalizer
均衡器

* Audio element
对于没有WebAudio的浏览器实现, 声音回退的功能



# 参考 #
<http://www.wavesurfer.fm/>
