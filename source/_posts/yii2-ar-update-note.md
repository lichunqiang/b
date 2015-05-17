title: "关于ActiveRecord之update需要注意的地方"
date: 2015-05-08 23:14:19
tags: [Yii2]
category: [Yii2]
---

开发中遇到使用 ActiveRecord::update 来更新数据，那么执行 update 的时候都做了什么：

* 检查属性的值是否发生改变，只有属性值改变的属性才保存

* 存在更新但不会对数据行产生影响的情况，此情况下返回受影响的行数为0

根据以上，我们在使用的时候需要注意的有：

```
 $user = User::findOne();

 $user->status = 1; //假设原值也为1

 $user->updated_at = now();

 if ($user->update() === false) { //注意这里必须要进行严格的判断「update执行返回的是受影响的行数」
 	//update faild
 } else {
 	//update success
 }

```