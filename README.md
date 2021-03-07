
## 拓展了Android OS MessageQueue

### Dispatch Queue是一个扩展Android MessageQueue的超高性能队列，类似协程支持同步返回，同时也支持异步执行，可包装主线程,灵活调度


## 引入

* Gradle


```gradle
implementation 'com.github.Justson:dispatch-queue:v1.0.5'
```


## 特性

* 线程可以配对交换元素在同步点
* 线程空闲后执行task
* 阻塞执行
* 线程之间交叉执行


## 控制线程执行顺序

``` 
        final StringBuffer output = new StringBuffer();
        AsyncTask.execute(() -> {
            output.append(1).append(", ");
            GlobalQueue.getMainQueue().postRunnableBlocking(() -> {
                // do you work , in the main-Thread
                output.append(2).append(", ");

            });
            output.append(3).append(", ");

            // invoke in the main-Thread and return a string data
            String message = GlobalQueue.getMainQueue().call(() -> {
                output.append(4).append(", ");
                return "hello world";
            });
            output.append(5).append(" data ").append(message);
            System.out.println(output.toString());
            // output  the order "1 2 3 4 5"
        });
```

```java
 output the order :1, 2, 3, 4, 5 
```



## 创建 DispatchThread 

```
        DispatchThread messageDispatch = DispatchThread.create("message");

        messageDispatch.postRunnable(() -> {
            // do you work , work in message thread
        });

        System.out.println("1");
        messageDispatch.postRunnableScissors(() -> {
            System.out.println("2");
        });
        System.out.println("3");
        // output 1 2 3

        // from message thread get a number, it will blocking until working finish.
        int i = messageDispatch.call(() -> 1);
```



## 线程空闲执行

```
        messageDispatch.postRunnableInIdleRunning(() -> {
            // do your work , when the message thread idle will callback this runable
        });
```



