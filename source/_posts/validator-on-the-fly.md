title: "Validator on the fly"
date: 2016-05-09 16:53:42
tags: [Yii2]
category: [Yii2]
---

首先从一个现实的需求中说起，在接口业务中有多个版本，突然在开发第三个版本的
时候其中有一条需求就是把用户提交的评分值由原来的允许的`1, 2, 3, 4, 5` 改成允许输入 `0, 2, 4, 6, 8, 10`

自定义的`validator`:

```
//ScoreValidator.php
class ScoreValidator extends Validator
{
    protected function validateValue($value)
    {
        if (in_array($value, [1, 2, 3, 4, 5])) {
            return null;
        }

        return [$this->message, []];
    }
}
```

```
//User.php
public function rules()
{
    return [
        ...
        ['rate', ScoreValidator::className()],
        ...
    ];
}
```


那么应该如何做兼容性，来保证多个版本的接口中都按照规则来赋值呢？我第一个想到的是利用di来解决。

为什么这么想？可以参考下之前的[一篇文章](http://blog.lzuer.net/2015/05/19/one-using-of-yii2-container/)

那么我们来具体实施.

首先，接口版本控制的结构是遵循官方来的：

```
modules
--v1
   -- controllers
   -- ...
   -- Module.php
--v2
   -- controllers
   -- ...
   -- Module.php
```

在这之前我们需要对之前定义的`ScoreValidator`进行下修改：

```
//ScoreValidator.php
class ScoreValidator extends Validator
{
    public $ranges = [0, 2, 4, 6, 8, 10];// 这里直接改成满足现有需求的数据区间

    protected function validateValue($value)
    {
        if (in_array($value, $this->ranges)) {
            return null;
        }

        return [$this->message, []];
    }
}
```

我们希望在v2版本的接口中来实现默认值的修改, 那么可以在原来的`v1`版本下的`Module::init`中来配置:

```
class Module extend \yii\base\Module
{
    public function init()
    {
        parent::init();

        Yii::$container->set(ScoreValidator::className(), [
            'ranges' => [1, 2, 3, 4, 5]
        ]);
    }
}
```

这样在`v1`接口下的涉及到`ScoreValidator`验证的地方就会按照这个规则来进行验证了。同样新的需求也可以兼顾到。

利用Yii2的容器和依赖注入可以很轻松写意的实现更多，还需要我们不停的探索！
