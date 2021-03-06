

作为一个从本科电子信息工程转入计算机研究生的同学，一直研究嵌入式、信号处理相关方向，一直被我奉为最经典的语言就是C和Matlab。

有了它们，我几乎可以干任何我想干的事情。

用多了Matlab做仿真，有时就会遇到：Matlab如何实现的定时器的功能的呢？比如：

1. 网络拓扑结构每隔10s更新一次
2. 让这个程序在5s后就结束吧

在Matlab中提供了一个多线程的方法——Timer。


每当使用一个新的Matlab函数，最好资料就是帮助文档：


```
>> help timer 
>> doc timer 
```

使用Timer的步骤如下：

## 1 创建Timer对象

timer对象的创建直接通过timer函数完成的，

```
iter     = 400; 
interval = 0.5;  % s
t = timer('TimerFcn', @caputure, ...  % 回调函数caputure，必选参数
    'Period', interval, ...  % 每隔interval (s)调用一次caputure函数
    'ExecutionMode', 'fixedSpacing', ...
    'TasksToExecute', iter); % 调用caputure次数设定为iter
```

Timer对象的格式为：

```
>> t

   Timer Object: timer-1

   Timer Settings
      ExecutionMode: fixedSpacing
             Period: 0.5
           BusyMode: drop
            Running: off

   Callbacks
           TimerFcn: @main/caputure
           ErrorFcn: ''
           StartFcn: ''
            StopFcn: ''
```

- Timer Settings中比较重要的参数有：

	- ExecutionMode   执行的模式

	就是确定TimerFcn执行周期计算的始末点

	'singleShot'    ：只能执行一次，故Period属性不起作用，其他模式都可以执行多次

	'fixedSpacing'  ：上一次TimerFcn执行完毕时刻到下一次TimerFcn被加入队列时刻之间的间隔

	'fixedDelay'    ：上一次开始执行到下一次被加入队列之间的间隔      
	 
	'fixedRate'     ：前后两次被加入到执行语句队列时刻之间的间隔

	- Period  时间间隔(执行周期)

	也就是每个Period执行一次TimerFcn，执行周期到底指的是哪两个时刻之间的间隔由ExecutionMode决定

	- StartDelay   启动时延

	从启动Timer开始到第一次把TimerFcn的加入到Matlab的执行语句队列中去的时延, 默认值为0s           

	- TasksToExecute    执行次数

	TimerFcn被执行的次数，默认为1次，设置多次时需要设置执行周期Period

- Timer对象的另一部分是回调函数（Callbacks）

	- TimerFcn    Timer函数
	
	是Timer对象的核心，Timer的多线就是通过执行这个TimerFcn来实现的，也是Timer对象的必选参数

	- ErrorFcn
	
	- StartFcn   启动Timer时调用的函数，可以用于初始化
	
	- StopFcn	 停止Timer时调用的函数

## 2 设定和读取Timer对象的参数

除了可以在创建Timer时设定相关参数外，还有其它的方法可以随时设置：

- 使用set/get函数

```
%创建一个默认的Timer对象
TaskTimer=timer
set(TaskTimer,'Name','Matlabsky','TimerFcn',@caputure)
get(TaskTimer,'Name')
````

- 直接操纵结构体

```
TaskTimer=timer
TaskTimer.Name='Matlabsky'
TaskTimer.TimerFcn=@caputure
```
	
## 3 创建回调函数

回调函数的格式为：

```
function my_callback_fcn(obj,event,p1,p2)
% by dynamic
% see also http://www.matlabsky.com
% 20092.15
%
% ==== obj和event为必选输入参数
%
% 1. obj就是前面创建的Timer对象，它包含着Timer的所有参数
%
% 2. event是一个结构体，包含Type和Data两个字段
%    Type保存的是当前的Timer对象执行的时间，比如StartFcn、StopFcn等
%    Data是一个结构体，包含time一个字段，保存的是执行该事件的系统时间
%
%    所以event的使用是event.Type获取Timer到底在执行哪个事件，event.Data.time获取执行该时间的系统时间
%
% ==== p1,p2...等是其它自定义的输入参数，根据需要在自己添加
```




# 参考

1. http://www.matlabsky.com/thread-646-1-1.html



