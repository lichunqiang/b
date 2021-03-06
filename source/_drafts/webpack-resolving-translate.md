webpack的模块解析
------

解析过程非常的简单。这里重点介绍以下三种请求：

* 绝对路径：`require('/home/me/file')`
* 相对路径: `require('../src/file')`  `require('./file')`
* 模块路径: `require('module')` `require('module/lib/file')`

### 解析绝对路径

首先检查路径是否指向一个目录。如果是一个目录我们需要找到这个目录下的主文件。因此`package.json`中的`main`字段值会和该路径连接起来。如果没有`package.json`或者`main`字段，`index`将被当做文件名使用。

我们已经将绝对路径转化成一个具体的文件。然后我们在被所有的文件扩展名(配置文件中的`resolve.extensions`字段).第一个存在的文件将会被当做最终查找结果

### 解析相对路径

目录的上下文是包含`require`声明的资源文件的目录。如果没有资源文件那么配置文件中的`context`字段被用来当做目录上下文。(这可能发生在入口文件或者loader产生的文件上)

相对路径将和目录上下文相接，最终确定文件的绝对路径。

### 解析模块路径

为了解析一个模块我们首先获取到在目录上下中所有的模块文件夹。这个过程和 nodejs的解析过程一样，但是查找的目录是配置文件中`resolve.moduleDirectories`所配置的目录。除了这个，`resolve.root`字段值附加到前面并且`resolve.fallback` 附加到后面.

通过依次查找每个模块目录并按照查找绝对路径的规则去查找。以此类推。

### 别名

有一个配置项`resolve.alias`可以重命名模块。

当尝试解析一个模块的路径时，如果模块名匹配`resolve.alias`中定义的一项，那么它将会被alias指定的所替换

### 缓存

所有的查找过程(遍历的目录等)都将会缓存起来，增加效率。

[原文](http://webpack.github.io/docs/resolving.html)