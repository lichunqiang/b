
## 使用Symfony的[DomCrawler](https://github.com/symfony/DomCrawler)组件分析HTML元素


[doc](http://symfony.com/doc/current/components/dom_crawler.html)


## example



## 中文编码

DomCrawler解析后的对像是[DomElement], 所以在解析中文时需要进行`utf8_decode`:

```
$html = <<<'HTML'
<!DOCTYPE html>
<html>
    <body>
        <p class="message">Hello World!</p>
        <p>Hello Crawler! 中文编码</p>
    </body>
</html>
HTML;

$crawler = new Crawler($content);

$node = $crawler->filter('body p')->last();

$value = utf8_decode($node->text());
```