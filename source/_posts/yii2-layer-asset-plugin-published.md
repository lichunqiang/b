title: "yii2-layer发布"
date: 2015-04-25 00:10:27
tags: Yii2
category: Yii2
---

贤心的最懂你的、跨终端的web弹层组件[layer](https://github.com/sentsin/layer)由于没有发布到bower，所以为了在Yii2的项目中方便使用,特发布了 [light/yii2-layer](https://packagist.org/packages/light/yii2-layer)

这样我们就可以愉快的依赖 layer 这块强大的插件了。

### 安装

```bash
composer require light/yii2-layer=*
```

### 使用

```php
use light\assets\LayerAsset;

LayerAsset::register($this);
```

或者在移动端使用：

```php
use light\assets\LayerMobileAsset;

LayerMobileAsset::register($this);
```

具体这款插件有多炫酷，还是自行到官方站去体验吧！ http://sentsin.com/jquery/layer/

### 说明

这里没有封装使用插件，因为目前还是在js中主动调用 - - ！