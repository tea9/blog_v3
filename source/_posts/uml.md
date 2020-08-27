---
title: 时序图
tags:
  - 时序图
abbrlink: 1900333125
date: 2019-07-19 17:38:59
description: more
---


## 时序图

时序图用于描述对象之间的传递消息的时间顺序, 即用例中的行为顺序.  

当执行一个用例时, 时序图中的每条消息对应了一个类操作或者引起转换的触发事件.  

在 UML 中, 时序图表示为一个二维的关系图, 其中, 纵轴是时间轴, 时间延竖线向下延伸.   横轴代表在协作中各个独立的对象. 当对象存在时, 生命线用一条虚线表示, 消息用从一个对象的生命线到另一个对象的生命线的箭头表示. 箭头以时间的顺序在图中上下排列.  


对象: 时序图中对象使用矩形表示, 并且对象名称下有下划线.   将对象置于时序图的顶部说明在交互开始时对象就已经存在了. 如果对象的位置不在顶部, 表示对象是在交互的过程中被创建的.  

生命线: 生命线是一条垂直的虚线. 表示时序图中的对象在一段生命周期内存在.   每个对象底部中心的位置都带有生命线.  

消息: 两个对象之间的单路通信. 从发送方指向接收方. 在时序图中很少使用返回消息.  

激活: 时序图可以描述对象的激活和钝化. 激活表示该对象被占用以完成某个任务.   钝化指对象处于空闲状态, 等待消息. 在 UML 中, 对象激活时将对象的生命线拓宽为矩形来表示的. 矩形称为计划条或控制期.   对象就是在激活条的顶部被激活的. 对象在完成自己的工作后被钝化.  

对象的创建和销毁: 在时序图中, 对象的默认位置是在图的顶部.   这说明对象在交互开始之前就已经存在了. 如果对象是在交互过程中创建的, 那么就应该将对象放到中间部分. 如果要撤销一个对象, 在其生命线终止点处放置 “ X” 符号.  



## Process On创建时序图
新建-UML


## LINKS
[processon](https://www.processon.com/)  
[ML建模之——时序图(待整理)](https://www.cnblogs.com/xykjlcx/p/8514583.html)  
[UML系列——时序图（顺序图）sequence diagram](https://www.jianshu.com/p/aab8f91ce978)  
[时序图、流程图、状态图、协作图之间的区别](https://blog.csdn.net/rosekin/article/details/14519277)  
[markdown进阶语法--时序图](https://www.kancloud.cn/yanshandou/kam2/598851)  
[时序图-登录](https://www.processon.com/view/5ac98d94e4b0cfe274871107)  
[【UML 建模】在线UML建模工具 ProcessOn 使用详解](https://blog.csdn.net/shulianghan/article/details/47684729)  
[UML类图（Class Diagram）中类与类之间的关系及表示方式](https://blog.csdn.net/a19881029/article/details/8957441)  
[用在线工具processOn画UML的用例图和时序图](https://www.cnblogs.com/zjh55/p/6834806.html)  

