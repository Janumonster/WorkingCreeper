# Android ICP机制

> IPC(Intent-Process Communication)，指进程间通信或跨进程通信，两个进程间进行数据交互的过程。



#### 进程和线程的区别

线程是CPU调度的最小单元，同时线程是一种有限的系统资源。

进程是指一个执行单元，如PC或移动端的一个程序或者应用。

一个进程可以包含多个线程，最简单情况下一个进程只有一个主线程，在android中叫做UI线程。



#### IPC基础概念介绍

> Serializable接口、Parcelable接口以及Binder。

**Serializable接口** 

Serializable是java所提供的一个序列化接口，是一个空接口，为对象提供标准的序列化和反序列化操作。

> 序列化指将对象的状态信息转换为可以存储或传输的形式的过程。 

