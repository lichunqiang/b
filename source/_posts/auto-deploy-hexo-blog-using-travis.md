title: "配置travis来自动发布hexo博客"
date: 2016-01-18 23:59:20
tags: [hexo, travis]
category: [hexo, travis]
---

首先在[travis](https://travis-ci.org)将你的项目加入到travis。

打开设置，添加token变量。

![add-token](http://7xq6zf.com1.z0.glb.clouddn.com/travis-setting.png)


在这之前我们需要在[github](https://github.com/settings/tokens), 生成一个token。

![generate-token](http://7xq6zf.com1.z0.glb.clouddn.com/generate-github-token.png)

在项目的 `.travis.yml` 中可以进行如下配置：

	language: node_js
	sudo: false
	node_js:
	  - 5.3.0

	before_install:
	  - git config --global user.name "lichunqiang"
	  - git config --global user.email "light-li@hotmail.com"
	  - git config --global push.default simple

	install:
	  - npm install hexo-cli -g

	script:
	  - git clone https://${token}@github.com/lichunqiang/lichunqiang.github.io.git .deploy_git -v
	  - git --version
	  - git remote add -f b https://${token}@github.com/lichunqiang/b.git
	  - git fetch b
	  - npm install

	  - hexo g
	  - cp -r public/* .deploy_git/
	  - cd .deploy_git
	  - git add -A
	  - git commit -am "Auto deploy from Travis-CI."
	  - git push --force -q

这样就等着 `travis` 构建完成之后，我们就可以看到文件就会自动发布了。
