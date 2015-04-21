title: "怎样将本地包安装到全局"
date: 2015-04-22 00:25:15
tags: NodeJS
category: [NodeJS, javascript]
---

1. In package directory

```
$ npm link
```

This createsa symlink to the current folder in npm's global installation directory.

2. Somewhere else, where you want to use the modules:

```
$npm  link <pkgname>
```

This will  create a symlink in your project's `node_modules/` folder to the global installation.
