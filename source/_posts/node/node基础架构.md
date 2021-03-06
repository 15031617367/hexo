---
title: Node基础服务架构
categories: []
date: 2020-10-24 10:30:35
tags: node
keywords: 前端 
---
# Node基础服务架构
1. JS如何调用C++
2. libuv & V8的功能
3. Event Loop 简单介绍
    
## 1.JS如何调用C++
  ### 1.1 Node.js是什么   
它是将多种技术组合起来,让JavaScript也能调用系统接口,开发后端应用.
  ### 1.2 Node用的了哪些技术  
- V8引擎
- libuv 
- C/C++实现的 c-ares、http-parser、OpenSSL、zlib    
  ### 1.3 架构图   
![avatar](./node基础架构/jiagoutu.png)
- bindings
    - node.js用C++对http_parser进行了封装，是他符合某些要求，封装的文件叫做http_parser_bindings.cpp;并编译成.node文件，供给js调用.    
## 2.libuv & V8的功能
  ### 2.1 libuv是什么
- 为了一个跨平台的异步I/O库,开始写libuv
    - libuv会根据系统自动选择合适的方案
- 功能
    -   可以用于TCP/UDP/DNS/文件等异步操作
  ### 2.2 V8是什么
- 功能
  - 将JS元代吗编程本地代码并执行
  - 维护调用栈,确保JS函数的执行书顺序
  - 内存管理,为所有对象分配内存
  - 实现JS的标准库
- 注意
  - V8不提供DOM API
  - V8 执行JS是单线程的
  - 可以开启两个线程分别执行JS
  - V8本身是包含多个线程的,如垃圾回收为单独线程
  - 自带event loop 但 Node.js基于libuv自己做了一个
## 3.Event Loop 简单介绍
  ### 3.1 EVent 是什么
简单理解成事件    
分为内部事件(计时器) 外部事件(文件读写\scoket连接)
  ### 3.2 Loop 是什么
简单理解成循环     
由于时间是分优先级的,所以按顺序处理;    
Node需要按顺序轮询每种事件;
  ### 3.3 Event Loop
操作系统可以出发事件,js去处理事件    
Event Loop就是对时间处理顺序的管理
    

