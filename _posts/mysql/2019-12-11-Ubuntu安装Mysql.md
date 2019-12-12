---
layout: post
title: Ubuntu安装MySQL
date: 2019-12-11 10:07:18
description: Ubuntu安装MySQL
share: true
tags:
 - mysql
comments: true
toc: false
---

## APT安装MYSQL

在此记录安装过程，以免重复Google。  
版本信息：Ubuntu 18.04、Mysql 8.0

### 1.安装mysql-apt-config.deb包进行APT源设置

>bash
{:.filename}
{% highlight bash %}
sudo dpkg -i mysql-apt-config_0.8.6-1_all.deb
{% endhighlight %}
进入图形配置界面选择ok

### 2.开始安装mysql-server

更新软件列表
>bash
{:.filename}
{% highlight bash %}
sudo apt-get update
{% endhighlight %}

开始安装  
>bash
{:.filename}
{% highlight bash %}
sudo apt-get install mysql-server
{% endhighlight %}
安装过程中输入root密码  

开机启动
>bash
{:.filename}
{% highlight bash %}
systemctl start mysql
{% endhighlight %}

创建用户  
>bash
{:.filename}
{% highlight bash %}
create user 'abc'@'%' identified by '123456';
{% endhighlight %}

授权  
>bash
{:.filename}
{% highlight bash %}
grant select,update,delete,create,drop on *.* to abc@"%";
{% endhighlight %}

刷新
>bash
{:.filename}
{% highlight bash %}
flush privileges;
{% endhighlight %}

## 3.Over
