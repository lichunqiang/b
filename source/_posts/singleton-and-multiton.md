title: "设计模式之单例和多例"
date: 2016-06-23 09:26:12
tags: [PHP, 设计模式]
category: [PHP, 设计模式]
-------

单例模式和多例模式都禁止外界将其实例化，通过静态工厂方法向外界提供循环使用的自身实例。

## 单例模式

```
class Singleton
{
	/**
	 * @var static
	 */
	private static $instance;

	public static function getInstance()
	{
		if (null === static::$instance) {
			static::$instance = new static();
		}

		return static::$instance;
	}

	//单例模式不允许被new
	private function __construct(){}

	//不允许被clone
	final public function __clone()
	{
		throw new \Exception('This is singleton, Clone is forbidden.');
	}

	//不允许被unserialize
	final public function __wakeup()
	{
		throw new \Exception('This is singleton, __wakeup is forbidden.');
	}
}
```

## 多例

```
class Multiton
{
	const INSTANCE_1 = '1';

	const INSTANCE_2 = '2';

	private static $instances = [];

	private function __construct(){}

	private function __clone(){}

	private function __wakeup(){}

	public static function getInstance($instanceName)
	{
		if (!array_key_exists($instanceName, self::$instances)) {
			self::$instances[$instanceName] = new self();
		}

		return self::$instances[$instanceName];
	}
}
```

## 总结

单例模式的主要优点：

	* 提供了对唯一实例的受控访问
	* 由于在系统中只存在一个对象，因此可以节约资源，对于一些需要频繁创建和销毁的对象可以提高系统性能
	* 允许可变数目的实例

主要缺点：

	* 单例模式没有抽象层，因此扩展很麻烦
	* 职责过重，一定程度上违背了“单一职责原则”
	* 滥用会带来一些负面问题