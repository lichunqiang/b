title: "怎么控制用户一定时间未操作自动登出"
date: 2015-06-04 22:35:11
tags: [Yii2]
category: [Yii2]
---

现在需求是：__如果用户已经登录，但是超过一定时间未操作「比如5分钟」，当用户再次进行操作时则需要重新登录__

我们可以通过配置  `yii\web\User::$authTimeout`来达到效果，上代码：

```
'user' => [
    'identityClass' => 'app\models\User',
    'authTimeout' => 5 //为了测试，设置5秒
],
```

还有一个更设定是可以让用户登入多长时间后，不管有没有操作，超过这个设定的时间后都会登出，上代码:

```
'user' => [
    'identityClass' => 'app\models\User',
    'absoluteAuthTimeout' => 20 //这里为了测试，设置为20秒，登入后不停刷新模拟用户操作，结果可看到，虽然在不断操作，但是过了设置的时间还是会自动登出
],
```

_需要注意的是如果设置 `enableAutoLogin = true` 的话，以上两个设置是不会生效的。_