---
title: Node.js学习笔记(一) 事件循环
date: 2018-06-16 15:33:56
tags: NodeJS
---

官方对Node.js的定义是：

* 基于Chrome V8引擎的JavaScript运行环境
* 使用高效、轻量级的事件驱动、非阻塞I/O模型

不同于C，C++，JAVA等编译型语言（需要对应的编译器编译后才能执行），JavaScript这类在运行时才解释执行的称为解释型语言，由于大部分的JavaScript都在浏览器中执行，所以对应的解释器一般都嵌入到浏览器内部。V8即是一款最初服务于Chrome的开源引擎。同时也被其他项目使用，比如Node.js。

{% asset_img /0610/node-Framework.png %} 

上图为Node.js的架构，除了顶层组件使用JavaScript编写，其他层使用C/C++编写：

* 顶层的Node.js API直接暴露给外界调用
* 中间层的Node.js Bingdings负责将更底层的系统API提供给顶层调用，C/C++ Addons是为了便于对Node进行扩展的接口
* 第三层是Node运行的核心，包括6大块

> V8 提供JavaScript运行环境
>
> LibUv 提供了事件循环(Event loop)，线程池(Thread loop)，异步IO(Async I/O)等核心功能
>
> c-ares 提供异步处理DNS解析的能力
>
> http parser 负责解析HTTP请求与响应
>
> OpenSSL 负责数据传输的安全性，以及提供一些必要的加密算法支持
>
> zlib 通用数据压缩库

其中，当V8解释执行JavaScript的同时，LibUv处理事件循环和I/O操作。

1. 非阻塞I/O

   阻塞与非阻塞指的是线程在等待响应时的状态。

   阻塞指在等待响应时会导致当前线程被挂起，调用线程在得到消息之后才会继续执行。非阻塞在等待响应时不会阻塞当前线程，而是继续处理其他请求。

2. 事件驱动

   在浏览器窗口或文档中发生的互动如点击、输入等用户操作称为事件。在node中各种操作如文件读写、请求收发等动作被认为是事件。通常会为事件绑定一系列的操作流程，当触发不同事件时便执行相应的流程。

   事实上，但凡满足既是单线程又是异步的语言都使用事件驱动(event-driven)。

3. 事件循环

   所有任务分为两种，一种是同步任务，指的是在执行栈(execution context stack)上排队的任务，一种是异步任务，指的是不进入执行栈而进入任务队列(tast queue)的任务。

   执行栈优先处理同步任务，当执行栈中的任务执行完毕后会读取任务队列中处理完成的异步任务，执行事件对应的回调函数。由此重复便形成了事件循环(event loop)。

   需要注意的是，事件循环是单线程实现异步的一种机制，并不是node特有。不过Node.js的事件循环的运行机制不同于JavaScript运行的浏览器环境。

   {% asset_img /0616/node-event-loop.png Node.js事件循环 %} 

   {% asset_img /0616/node-run-task.png Node.js执行栈 %} 

   {% asset_img /0616/browser-event-loop.png JavaScript在浏览器中的事件循环 %} 

   

   
