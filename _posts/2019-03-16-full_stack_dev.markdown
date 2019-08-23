---
layout: post
title: "对FreeRadius二次开发整套系统搭建"
date: 2019-03-16 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - Python
       
---

# 搭建全栈开发框架

该开发框架前端基于Vue，后端基于Django，所以该项目可以跨平台，不论是什么操作系统，安装Node.js与Python即可开启项目的搭建与开发。搭建环境分为三大块：

1. 创建虚拟环境
1. 搭建后端框架
1. 搭建前端框架

## 1 创建虚拟环境 virtualenv
### virtualenv的安装与使用
### 1.1 virtualenv的安装

	pip install virtualenv
	sudo pip install virtualenvwrapper
	
`Virtaulenvwrapper`是`virtualenv`的扩展包，用于更方便管理虚拟环境，它可以做：

* 将所有虚拟环境整合在一个目录下
* 管理（新增，删除，复制）虚拟环境
* 快速切换虚拟环境

	
### 1.2 安装过程出现问题与解决办法
出现的问题：

	Cannot uninstall 'six'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
	
解决办法：

	sudo pip install tld

	sudo pip install tld --ignore-installed six --user
	
	sudo pip install virtualenvwrapper

### 1.3 virtualenv的使用
创建目录

	mkdir /Users/didi/workspace/virtualenv

创建虚拟环境

	mkvirtualenv env_name

注意 一定要制定Python版本

	mkvirtualenv -p python3 env_name

### 1.4 环境变量配置
创建文件

	touch ~/.bashrc
	
添加：
	
	export WORKON_HOME=/Users/didi/workspace/virtualenv
	source /usr/local/bin/virtualenvwrapper.sh

每次打开终端，执行命令如下，
`source ~/.bashrc`

### 1.5 继续学习virtualenv命令：

`lsvirtualenv` `workon` `lssitepackages` `cdvirtualenv`

	╰─$ lsvirtualenv -b
	apis_django_vue
	
	╰─$ workon apis_django_vue
	(apis_django_vue) ╭─didi@localhost ~
	
	╰─$ lssitepackages
	easy_install.py             pip                         pkg_resources               setuptools-40.8.0.dist-info wheel-0.33.1.dist-info
	easy_install.pyc            pip-19.0.3.dist-info        setuptools                  wheel
	
	╰─$ cdvirtualenv
	(apis_django_vue) ╭─didi@localhost ~/workspace/virtualenv/apis_django_vue
	╰─$ lssitepackages
	easy_install.py             pip                         pkg_resources               setuptools-40.8.0.dist-info wheel-0.33.1.dist-info
	easy_install.pyc            pip-19.0.3.dist-info        setuptools                  wheel

复制虚拟环境：

	$ cpvirtualenv env1 env3
	Copying env1 as env3...

退出虚拟环境：

	deactivate
	
删除虚拟环境：

	$ rmvirtualenv env2
	Removing env2...

## 2 搭建后端框架

### 2.1 创建后端环境

	cd /Users/didi/workspace/virtualenv
	
	mkvirtualenv full_stack_project
	
	workon full_stack_project
	
	cdvirtualenv
	
### 2.2 安装Django rest framework

创建目录

	mkdir pproject
	
目录结构如下：

	drwxr-xr-x  26 didi  staff   832B  2 28 14:44 bin
	drwxr-xr-x   3 didi  staff    96B  2 28 14:23 include
	drwxr-xr-x   3 didi  staff    96B  2 28 14:23 lib
	drwxr-xr-x   4 didi  staff   128B  2 28 17:26 project

bin include lib 是virtualenv 创建自带的

	cd project
	
	# Install Django and Django REST framework into the virtualenv
	
	pip install django
	pip install djangorestframework

	# Set up a new project with a single application
	
	django-admin startproject backend .  # Note the trailing '.' character
	cd backend
	django-admin startapp apis
	cd ..

目录结构如下：	
	
![目录结构](http://niubencoolboy.github.io/img/blogs/backend_dirStruct.png)

### 2.3 Django Rest Framework 编程

参考官网教程：[https://www.django-rest-framework.org/]()


### 2.4 遇到的问题与解决办法

#### 2.4.1 Python2 与 Python3 问题

##### 2.4.1.1 Windows安装Python3问题

直接安装最新版的`anaconda3` 去官网下载，安装好，Python3自带已经安装好了，省去了各种环境变量的配置。
anaconda的使用与pip很像，几分钟入门。
使用参考：

1. [https://www.jianshu.com/p/2f3be7781451]() 使用这个链接修改镜像就行
1. [https://www.jianshu.com/p/2057ca7680b4]() 使用这个链接创建虚拟环境

##### 2.4.1.2 Mac 升级 Python3 问题

brew install

	brew install python3
	
出现错误如下：

	==> Pouring python-3.7.2_1.mojave.bottle.1.tar.gz
	Error: An unexpected error occurred during the `brew link` step
	The formula built, but is not symlinked into /usr/local
	Permission denied @ dir_s_mkdir - /usr/local/Frameworks
	Error: Permission denied @ dir_s_mkdir - /usr/local/Frameworks
	
解决办法：

	sudo install -d -o $(whoami) -g admin /usr/local/Frameworks
	
再次安装：

	brew install python3
	
出现如下提示：

	Warning: python 3.7.2_1 is already installed, it's just not linked You can use `brew link python` to link this version.
	
执行命令：

	brew link python
	
测试：

	╰─$ python3
	Python 3.7.2 (default, Jan 13 2019, 12:50:01)
	[Clang 10.0.0 (clang-1000.11.45.5)] on darwin
	Type "help", "copyright", "credits" or "license" for more information.
	>>>
	
##### 2.4.1.3 linux 安装 Python3 问题

	wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
	
	tar zxvf Python-3.7.2.tgz 
	
	cd Python-3.7.2
	
	./configure   
	make all   
	make install 
	
出现错误：

	ImportError: No module named '_ctypes'
	
解决办法：

	yum -y install libffi-devel
	make install 

建立软连接：

	ln -s /usr/local/bin/python3.7 /usr/bin/python3 
	
	/usr/bin/python3 -V

#### 2.4.2 Django no such table

	python manage.py makemigrations
	python manage.py migrate --run-syncdb

#### 2.4.3 忘记用户名与密码：

使用如下命令进入shell：

`python manage.py shell`

	from django.contrib.auth.models import User 
	user =User.objects.get(username='administrator')  
	user.set_password('new_password')  
	user.save()
	
如果忘记用户名，可以使用如下命令查找：

	from django.contrib.auth.models import User
	user1 = User.objects.filter(is_superuser = True)
	print(user1)
	

## 3 搭建前端框架

### 3.1 搭建Vue框架
去 node.js 官网（[https://nodejs.org/en/]()）下载不同系统平台的安装包
下载安装后，在终端输入`npm -v `

	╰─$ npm -v                                                                  1 ↵
	6.8.0
修改 npm 源 改为淘宝，速度更快。

	npm install -g cnpm --registry=https://registry.npm.taobao.org
	
使用 npm 安装 webpack + vue

	cnpm install webpack -g
	
	cnpm install webpack-cli -g
	
	cnpm install vue -g
	
	cnpm install vue-cli -g
	
### 3.2 安装前端框架模板

	git clone https://github.com/ElementUI/element-starter
	
	npm install yarn -s
	
	npm install element-ui -s
	
	npm install iview --save
	
	npm install vue -s

将`package.json`中`devdependencies`中所有的依赖包进行安装。

	npm install autoprefixer babel-core babel-loader babel-preset-vue-app css-loader file-loader html-webpack-plugin postcss-loader rimraf style-loader url-loader vue-loader vue-template-compiler webpack webpack-dev-server -s
	
	npm run dev
	
 可以看到 Vue 的 logo主页
 
	
### 3.3 安装滴滴BASE UI组件

修改 npm 源为 `xiaojukeji`

	alias dnpm="npm --registry=http://registry.npm.xiaojukeji.com \
	--cache=$HOME/.npm/.cache/dnpm \
	--disturl=https://npm.taobao.org/mirrors/node \
	--userconfig=$HOME/.dnpmrc"

安装三个所需要的组件：	

	dnpm install @dt/bc-v-header
	
	dnpm install @dt/bc-v-table
	
	dnpm install --save-dev @dt/bc-core

再次将 npm 源 修改为淘宝

	npm set registry https://registry.npm.taobao.org

	npm install vue-router less less-loader -s
	
	npm install babel-plugin-component -D

	npm install babel-plugin-import --save-dev	
	npm install @antv/data-set --save

	npm install --save viser-vue
	
	npm run dev

安装好后的目录结构为：

![frontend目录结构](http://niubencoolboy.github.io/img/blogs/frontend_dirStruct.png)


