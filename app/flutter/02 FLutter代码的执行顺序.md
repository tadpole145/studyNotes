## 02 FLutter代码的执行顺序



dart才用单线程执行,包含2个任务队列

* 微任务队列

  ​           微任务队列包含有 Dart 内部的微任务，主要是通过 scheduleMicrotask 来调度。

* 事件队列

  ​			事件队列包含外部事件，例如 I/O 、 Timer ，绘制事件等等。

#### Dart 执行的逻辑过程

1. 首先是执行 main 函数，并生产两个相应的微任务和事件任务队列；

2. 判断是否存在微任务，有则执行，执行完成后再继续判断是否还存在微任务，无则判断是否存在事件任务；
3. 如果没有可执行的微任务，则判断是否存在事件任务，有则执行，无则继续返回判断是否还存在微任务；
4. 在微任务和事件任务执行过程中，同样会产生微任务和事件任务，因此需要再次判断是否需要插入微任务队列和事件任务队列。

> tips: 
>
> 1. 每当事件任务执行完成后,都要去检查微任务是否存在,如果存在,则优先执行微任务.
> 2. 当某个事件任务特别耗时的时候,dart会block导致卡顿





![Drawing 0.png](https://s0.lgstatic.com/i/image/M00/1D/7D/Ciqc1F7h_D2ARi2aAAJ2G36y8Ng725.png)



#### Isolate多线程

​		Dart 的单线程叫作 isolate 线程，每个 isolate 线程之间是不共享内存的，通过消息机制通信.

当多个线程同时存在微任务和事件任务时候,其内部的微任务与事件任务执行顺序并非固定的,取决于线程的耗时长短.具体案例参考如下:

```
import 'dart:async';
import 'dart:isolate';
Isolate isolate;
void main() {
	print('project start'); // 打印项目启动标记
	ctask(); // 分配并执行 C 任务
	// 大循环，等待
	//for(int i=0; i<1000000000; i++){
	//}
	// 执行判断为微任务，添加到微任务队列
	scheduleMicrotask((){
		// 执行判断为微任务，添加到微任务队列
		scheduleMicrotask((){
			print('h1-1 task complete'); // 执行微任务，并打印微任务优先级h1-1
		});
		// 执行判断为事件任务，添加到事件任务队列
		Timer.run((){
        	print('l1-1 task complete'); // 执行事件任务，并打印事件任务优先级l1-1
        });
        print('H1 task complete'); // 打印H1微任务执行标记
	});
	// 执行判断为微任务，添加到微任务队列
	scheduleMicrotask((){
		// 执行判断为微任务，添加到微任务队列
		scheduleMicrotask((){
			print('h2-1 task complete'); // 执行微任务，并打印微任务优先级h2-1
		});
		// 执行判断为事件任务，添加到事件任务队列
		Timer.run((){
        	print('l2-1 task complete'); // 执行事件任务，并打印事件任务优先级l2-1
        });
        print('H2 task complete'); // 打印H2微任务执行标记
	});
	// 执行判断为事件任务，添加到事件任务队列
	Timer.run((){
		// 执行判断为微任务，添加到微任务队列
		scheduleMicrotask((){
			print('h3-1 task complete'); // 执行微任务，并打印微任务优先级h3-1
		});
		// 执行判断为事件任务，添加到事件任务队列
		Timer.run((){
        	print('l3-1 task complete'); // 执行事件任务，并打印事件任务优先级l3-1
        });
		print('L3 task complete'); // 打印L3事件任务执行标记
    });
	
	// 执行判断为事件任务，添加到事件任务队列
	Timer.run((){
		// 执行判断为微任务，添加到微任务队列
		scheduleMicrotask((){
			print('h4-1 task complete'); // 执行微任务，并打印微任务优先级h4-1
		});
		// 执行判断为事件任务，添加到事件任务队列
		Timer.run((){
        	print('l4-1 task complete'); // 执行事件任务，并打印事件任务优先级l4-1
        });
		print('L4 task complete'); // 打印L4事件任务执行标记
    });
}
/// C 任务具体代码，创建新的线程，并监听线程返回数据 
void ctask()async{
	final receive = ReceivePort();
	isolate = await Isolate.spawn(doCtask, receive.sendPort);
	receive.listen((data){
        print(data);
	});
}
/// 创建的新线程，具体执行的任务代码
void doCtask(SendPort port){
	// 执行判断为微任务，添加到微任务队列
	scheduleMicrotask((){
		print('ch5-1 task complete'); // 执行微任务，并打印微任务优先级ch5-1 
	});
	// 执行判断为事件任务，添加到事件任务队列
	Timer.run((){
        print('cl5-1 task complete'); // 打印cl5-1事件任务执行标记
    });
	port.send('C1 task complete'); // 打印 C 任务执行标记
}

```

打印输出结果为:

```
project start
H1 task complete
H2 task complete
h1-1 task complete
h2-1 task complete
L3 task complete
h3-1 task complete
L4 task complete
h4-1 task complete
l1-1 task complete
l2-1 task complete
l3-1 task complete
l4-1 task complete
ch5-1 task complete
cl5-1 task complete
C1 task complete

```

