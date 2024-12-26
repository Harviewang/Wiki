---
title: nastools折腾
description: 
published: true
date: 2024-12-26T10:44:35.507Z
tags: 
editor: markdown
dateCreated: 2024-12-25T04:17:56.747Z
---

一直想折腾自动化影视系统，迫于搞不明白/没时间折腾pt作罢

今天刷张大妈的时候发现nt 2.9版本之前其实是支持bt的，另外有个人维护的版本目前也是支持bt的，遂准备在群晖上试试。

**1 查看项目及相关教程**

**1.1 涉及到的套件/服务如下：**

**1.1.1 核心组件** 

    **nastools** https://github.com/hsuyelin/nas-tools 最新版本3.4.1，且矿神套件有收录，可直接安装

**1.1.2 媒体服务器** 

    **jellyfin**

    **emby**

    **plex** 

**1.1.3 下载器**

    **qbittorrent**

    **transmission**

**1.1.4 索引器**

    ja 由于官方索引可能会出现无法找到资源的情况，需要添加索引

**他们之间的关系如下图所示：**

**1.2参考教程如下：**

https://blog.csdn.net/weixin\_66688931/article/details/136563912

https://doc.nastool.shop/

https://post.smzdm.com/p/a2xzmwrq/

**2 准备工作**

**2.1 获取密钥**

**2.1.1  注册TMDB并申请API的密钥**

注册用途：用于Nastool媒体库中所有影视资源的刮削（获取海报、简介、演员表、上映年份等）

[官方：https://www.themoviedb.org](https://www.themoviedb.org/)

![1](https://cdn.92cos.vip:18888/i/2023/10/19/TMDB.jpg)

申请API密钥时请填写以下内容：  
Use KeyYong to retrieve information about movies and television.These uses are for personal use and are not for commercial use.

手把手教你申请TMDB的API秘钥： [点击查看图文教程](https://cdn.92cos.vip:18888/i/2023/10/19/TMDB-API.pdf)

  
 

![1](https://cdn.92cos.vip:18888/i/2023/10/19/TMDB-API.png)

**2.1.2 微信消息推送PushPlus**

注册用途：用于NasTool的微信公众号消息推送

[PushPlus官网：http://www.pushplus.plus](http://www.pushplus.plus/)  
 

![1](https://cdn.92cos.vip:18888/i/2023/10/19/pushplus.jpg)

![2](https://cdn.92cos.vip:18888/i/2023/10/19/pushplus2.png)

**2.1.3 登录豆瓣帐号**

注册用途：用于配置豆瓣远程订阅 (豆瓣APP点击想看后，nastool会自动搜索下载对应的电影或者电视剧)

[豆瓣网： https://www.douban.com](https://www.douban.com/)

![3](https://cdn.92cos.vip:18888/i/2023/10/19/douban.jpg)

  
 

![2](https://cdn.92cos.vip:18888/i/2023/10/19/douban2.png)

2.1.4 密钥记录

themoviedb：e5cc4c8a6e8780f31897cdb6dccd9f31f

pushplus：1f47d7dbf857410ab7b4311af1ff5d910

豆瓣：666866816

**2.2 文件夹准备**

在资源池2中分别创建共享文件夹Docker和Video，并创建以下路径：

**Nastools** /Volume2/Docker/Nastools 用于放置nt的配置文件

**Download** /Volume2/Video/Download 用于放置下载的文件

    **电影** /Volume2/Video/电影/

    **电视剧** /Volume2/Video/电视剧/

    **动漫** /Volume2/Video/动漫/

    **MV** /Volume2/Video/MV/

    **未分类** /Volume2/Video/未分类/

3.安装和配置

使用矿神套件直接进行安装，需要按安装python 3.10.0，安装完毕后打开3003端口，账号admin密码password。

3.1 配置 themoviedb

输入key

3.2 配置 qb

3.3 配置索引

3.4 配置媒体服务器