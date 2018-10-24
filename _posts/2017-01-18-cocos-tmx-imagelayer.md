---
tags: [cocos2d-js]
title: 给cocos中TMXMap增加解析ImageLayer的功能
layout: post
author: samael
---

# 给cocos中TMXMap增加解析ImageLayer的功能

在制作瓦片地图时, 有时可能需要在瓦片地图下去插入一个背景, 如果是单一的大图背景, 我们可以直接在TMXMap的节点下插入一个图片就可以

| 图片 |
| :-: |
| 瓦片层 |

但是在有些需求中我需求的结构是这样的

| 瓦片层 |
| :-: |
| 图片 |
| 瓦片层 |

其实在中间也是可能通过简单的addChild去添加

``` javascript
    var layer = tileMap.getLayer('floor');
    layer.addChild(bg);
    layer.reorderChild(bg);
```

但是项目中有些背景纯放一张图, 虽然可行, 但是会让纹理大小没办法控制 比如

![](pic-blog.test.upcdn.net/2018/10/24/15403546257521.jpg)


背影图中间是个空的透明像素, 这样其实在中也会占用内存, 所以需要把图拆开然后再拼成需要的图案

TileMap编辑器中除了ObjectLayer，TileLayer之外还有一种叫ImageLayer的类型，主要用于TileMap的背景插入。

**注意** 
每个TMXLayer都是一个SpriteBatchNode, 所以在同一层中只能使用一张纹理

## cocos中生成TileMap的过程

``` c++
// CCTMXTiledMap.cpp
void TMXTiledMap::buildWithMapInfo(TMXMapInfo* mapInfo)
{
    _mapSize = mapInfo->getMapSize();
    _tileSize = mapInfo->getTileSize();
    _mapOrientation = mapInfo->getOrientation();

    _objectGroups = mapInfo->getObjectGroups();

    _properties = mapInfo->getProperties();

    _tileProperties = mapInfo->getTileProperties();

    int idx = 0;

    auto& layers = mapInfo->getLayers();
    for (const auto &layerInfo : layers) {
        if (layerInfo->_visible) {
        TMXLayer *child = parseLayer(layerInfo, mapInfo);
        if (child == nullptr) {
             idx++;
             continue;
        }
            
        addChild(child, idx, idx);
        // update content size with the max size
        const Size& childSize = child->getContentSize();
        Size currentSize = this->getContentSize();
        currentSize.width = std::max(currentSize.width, childSize.width);
            currentSize.height = std::max(currentSize.height, childSize.height);
            this->setContentSize(currentSize);
        }
        idx++;
    }
}

```

## 修改
因为只是根据自己的项目处理了一下代码, 这里只说一下基本的原理, 这里用XML举例:

``` c++
// CCTMXXMLParser.cpp
void TMXMapInfo::startElement(void *ctx, const char *name, const char **atts);

void TMXMapInfo::endElement(void *ctx, const char *name);
``` 

这两个函数是去对tmx文件进行解析, 并把相应的属性进行存放的.

cocos并没有对imagelayer的标签进行处理, 所以我们需要编写相应的代码进行.
相邻的imagelayer可以合并做个bakelayer进行同一的渲染

``` xml
tmx
 <imagelayer name="bg4" offsetx="1358" offsety="1092">
  <image source="background_1_4.png" width="940" height="511"/>
 </imagelayer>
 <imagelayer name="bg5" offsetx="2871" offsety="728">
  <image source="background_1_5.png" width="949" height="474"/>
 </imagelayer>
 <imagelayer name="bg1" offsetx="2296" offsety="574">
  <image source="background_1_1.png" width="575" height="520"/>
 </imagelayer>
```
## 注意
1. TileMap的中TileLayer的水平偏移与垂直偏移也没有处理
2. imagelayer的偏移的坐标原点与纹理锚点与cocos中也不一样, 需要对着纹理进行转换

``` c++
Sprite *child = Sprite::create(layerInfo->_sourceImage);
Size childSize = child -> getContentSize();
Size currentSize = this->getContentSize();
child->setAnchorPoint(Vec2::ZERO);
auto pos = Vec2(currentSize.width , currentSize.height);
pos = Vec2(layerInfo->_layerOffset.x, pos.y - layerInfo->_layerOffset.y - childSize.height);
child->setPosition(pos);
addChild(child, idx, idx);
```





