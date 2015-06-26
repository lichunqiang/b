title: "yii2之DynamicModel"
date: 2015-05-06 21:53:42
tags: [Yii2]
category: [PHP, Yii2]
---

### 什么是DynamicModel

DynamicModel是用来进行临时的数据验证，有些情况，我们不希望为一次数据提交建立专门的Model来处理「例如：操作只是进行更改商品的状态」.

### 如何来用

#### DynamicModel::validateData

DynamicModel提供了静态方法 `validateData` 来辅助我们实现动态验证，考虑一下代码：

```php
public function actionUpdateStatus($id, $status)
{
	$model = DynamicModel::validateData(compact('id', 'status'), [
		[['id', 'status'], 'required'],
		[['id', 'status'], 'integer'],
		['status', 'in', 'range' => [1, 2]],
		...
	]);

	if ($model->validate()) {
		//validation success
	} else {
		//validation error
	}
}

```

这里的 `validateData` 方法接受两个参数：model的属性值和对应的验证方法，它返回的是一个 DynamicModel的实例，拥有赋予的属性和对应的值，当然还有定义的验证方法。

#### 面向对象的方法

调用 `validateData` 方法，并传入属性值和验证方法会为我们自动生成一个 DynamicModel 的实力，当然我们也可以：

```php
$model = new DynamicModel(compact('id', 'status'));
$model->addRule(['id', 'status'], 'required')
	  ->addRule(['id', 'status'], 'integer')
	  ->addRule('status', 'in', ['range' => [1, 2]]);
	  ...

if ($model->validate()) {
	//validate success
}
...
```

### 更多

通过以上两个方法得到动态model后，我们可以通过 `$model->id` 的方式来获取对应的值。

当然也可以调用 `$model->attributes()` 来获取所有属性.

当希望为创建好的model新增属性时，我们可以：

```php
$model->defineAttribute('user_id', 1111);
$model->defineAttribute('name');
```

需要删除某一属性时：
```php
$model->undefineAttribute('name');
```