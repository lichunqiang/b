title: "在类之外调用私有方法"
date: 2015-09-18 00:17:39
tags: PHP
category: [PHP, 代码段]
---

```php
class Test
{
    private $prefix = 'Hello ';

    private function hello($name)
    {
        echo $this->prefix, $name, PHP_EOL;
    }
}
```
第一种方式：

```php
$class = new \ReflectionClass(Test::class);
$method = $class->getMethod('hello');
$method->setAccessible(true);
$method->invoke(new Test(), 'Light');
```

第二种方式:

```php
$class = new \ReflectionClass(Test::class);
$method = $class->getMethod('hello');
call_user_func($method->getClosure(new Test()), 'Light');
```
