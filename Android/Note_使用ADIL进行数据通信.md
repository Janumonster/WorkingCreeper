# Note_使用ADIL进行数据通信

ADIL来进行进程通信分为服务端和客户端向各方面：

##### 1、服务端

首先创建一盒Service用来监听客户端的链接请求，然后创建一个aidl文件，间暴露给客户端的接口在这里声明，最后在Service中实现这个接口。

##### 2、客户端

首先绑定Service，成功后间服务返回的Binder对象转化为aidl接口所属类型，然后就可以调用aidl的方法了。

> 实际实现过程会比较复杂，上述只为方便理解 

| AIDL文件支持哪些数据类型                                     |
| :----------------------------------------------------------- |
| 基本数据类型（int、long、char、boolean、double等）           |
| String和CharSequence；                                       |
| List：只支持ArrayList，且里面的你每个元素都必须能够被AIDL支持； |
| Map：只支持HashMap，且里面的你每个元素都必须能够被AIDL支持，包括key和value； |
| Parcelable：所有实现了Parcelable接口的对象；                 |
| AIDL：所有的AIDL接口本身也可以在AIDL文件中使用。             |

