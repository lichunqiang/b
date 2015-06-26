title: "Yii2中的Validators之BooleanValidator"
date: 2015-05-07 23:01:16
tags: [Yii2]
category: [Yii2, Yii2-validator]
---

顾名思义，从命名上来看，给人的感觉是进行布尔类型判断的。

但其实我们可以用它来做一些更有意思的比较。

比较常规的用法：

```php
public function actionSearch()
{
	$model = new DynamicModel(['status']);

	$model->addRule('status', 'boolean');
	$model->status = 1;
	$model->validate(); // true, status 设置为 0 也会验证为true

	$model->addRule('status', 'boolean', ['strict' => true]);
	$model->status = 1;
	$model->validate(); // false
	$model->status = '1';
	$model->validate(); // true
}
```

这里列举了两个验证场景：1是非严格比较， 2是严格比较。

在 BooleanValidator 中有两个属性分别代表比较的值，其默认为 `trueValue = '1'` 和 `falseValue = '0'`,

在验证的时候 BooleanValidator 分别对对相应的属性值进行比较，当有一个「trueValue或falseValue」分别在严格比较和非严格比较的情况下和属性值相等时则验证通过。

由此，我们可以通过设置 trueValue 和 falseValue 来进行更多的比较，例如：

```php
//此场景我们需要验证用户的输入为 yes 或者 no

$model = new DynamicModel(['action']);
$model->load(Yii::$app->getRequest()->post(), '');
$model->addRule('status', 'boolean', ['strict' => true, 'trueValue' => 'yes', 'falseValue' => 'no']);
if ($model->validate()) {
	//validate pass
} else {
	//validate error
}

```