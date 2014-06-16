---
layout: post
title: gstreamill
category: products
---

## 简介

gstreamill是基于gstreamer（1.0）的开源编码器，可以通过http的post方法向gstreamill提交job，让gstreamill完成实时编码、转码、录制等工作。其中实时编码类型的job已经支持，其它类型的job尚未支持。job是json格式的描述文件，用于描述“job”的技术细节。本文的目的是为了让读者了解和使用gstreamill。

gstreamill的特性

1. 基于gstreamer，可以利用gstreamer已有的plugin或者编写新的plugin实现对各种编码、封转及协议的支持。
1. 支持多码率（由job描述文件决定码率个数），支持GOP对齐。
1. 支持hls、http渐进下载以及udp等输出。
1. 通过RESTful的接口进行管理，便于集成到运营商的系统中。
1. 采用json格式的描述文件来描述要完成的工作 - job。
1. 具体的编码、转码等在子进程中实现，支持故障重新启动。
1. 通过gstreamer plugin支持丰富的输入源，比如Decklink SDI、v4l2类型的采集卡、UDP/RTP等。

## 应用

gstreamill典型的应用就是用于DVB/OTT网关：

与通常的编码器相比，最大的特点是可以通过管理接口对gstreamill发送job让它完成编码工作，这样可以比较容易实现编码集群，进而实现实时源的高可用方案。

## 安装

首先从https://github.com/zhangping/gstreamill下载gstreamill的源码，目前gstreamill只在ubuntu 13.10下进行过测试。要编译安装gstreamill，需要先安装如下软件包：

*. gnome-common
*. autoconf
*. automake
*. libtool
*. gstreamer-devel
*. gstreamer-plugins-base-devel
*. 准备好后，进入源码目录执行如下命令安装：

    ./autogen.sh
    ./configure (--help)
    make
    make install

## 使用

运行 gstreamill -h命令可以看到gstreamill命令行的相关参数

不加任何参数直接运行gstreamill就启动了gstreamill：

    gstreamill

停止gstreamill：

    gstreamill -s

通过管理端口指令gstreamill执行job，默认管理端口是20118，可以在启动的时候指定管理端口，这里使用的是curl工具通过http post向gstreamill发指令的，在源码的examples目录下有job的事例，建议从test.job入手：

    curl -H "Content-Type: application/json" --data @examples/test.job http://localhost:20118/start

在前台调试一个job：

    gstreamill -j job_descript_file

播放输出可以采用vlc等播放器，对于examples/test.job，支持http渐进播放和m3u8播放，url为，注意http输出的默认端口是20119，可以在启动gstreamill的时候通过命令行参数指定：

    http://localhost:20119/live/test/encoder/0
    http://localhost:20119/live/test/playlist.m3u8

## 管理接口

启动一个job

    curl -H "Content-Type: application/json" --data @examples/test.job http://localhost:20118/start

停止一个job

    curl http://localhost:20118/stop/job_name

查询gstreamill状态

    curl http://localhost:20118/stat/gstreamill curl http://localhost:20118/stat/gstreamill/livejob/test

## 输出接口

以下模式的输出接口均采用vlc进行了验证，注意，这里的job name是job文件中name的值

    http
    http://localhost:20119/live/job name/encoder/encoder_index

    hls
    http://localhost:20119/live/job name/playlist.m3u8
    或者
    http://localhost:20119/live/job name/encoder/encoder_index/playlist.m3u8

    udp
    udp://@ip:port

## JOB文件编写

要编写job文件需要对gstreamer有必要的理解，请参照源码examples目录下的示例文件。

{% include references.md %}
