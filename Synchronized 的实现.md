## synchronized同步锁的实现
java中的synchronized关键字用来处理java中多线程同步的问题，既可以用它声明一个被synchronized关键字标注的代码块，也可以用它直接标注静态方法或实例方法。具体对于该关键字如何使用，我们另行讨论。这篇文章的目的主要介绍一下该关键字是如何实现的。

#### 两个指令
##### monitorenter 与 monitorexit
首先编译下面的java代码，然后对生成的.class文件，执行指令`javap -verbose Test.class`。产生如下图所示的字节码，能够看到一个`monitorenter`对应两个`monitorexit`指令。这里的两个monitorexit指令，是为了保证在任何状况下，锁都能够正常退出。所以实际上`monitorenter`与`monitorexit`是一对一的关系。这两个指令各自会消耗操作数栈上的一个引用类型的元素，作为加解锁的锁对象。也就是下面代码中的lock对象。

    public class Test {
      public void test(Object lock) {
            synchronized (lock) {
                lock.hashCode();
            }
        }
    }
![](https://github.com/WalkingNL/Pics/blob/master/synchronized.jpg)

上面的代码中，用synchronized关键字标注的是对象。但如果用它标记方法时，见下面的代码。编译再执行javap指令，生成的字节码文件如下图。访问标记与上图相比有两点差别。第一，访问标记变了；第二，虚指令里无`monitorenter`与`monitorexit`了。一个一个来说，能够看到下图中访问标记(flags)后面多了ACC_SYNCHRONIZED。有了这个标记，意思是在进入到test()方法中时，java虚拟机进行monitorenter操作。在退出时，一定能保证执行monitorexit操作。只不过这种加锁的方式下，monitorenter与monitorexit操作对应的锁对象是隐式的。因为这里test()是实例方法，所以锁对象自然就是this；如果改成静态方法，锁对象就是类的Class实例。
    
    public class Test {
      public synchronized void test(Object lock) {
            lock.hashCode();
        }
    }
    
![](https://github.com/WalkingNL/Pics/blob/master/synchronized_mark.jpg)

#### 锁的类别

##### 重量级锁
最基础、最原始的锁。这种方式的加锁非常笨重，得依靠操作系统来完成。需要进行系统调用，开销非常大。对于不同的操作系统，调用的接口不同。例如，在类似许多诸如Linux这样的平台上，是通过调用pthread来实现的。如果频繁进行`阻塞`与`唤醒`这种昂贵的过程，代价无疑是巨大的。基于此，在重量级锁的情形下，为了能在一定程度上减少对线程的`阻塞`和`唤醒`，引入了一定的优化措施，如线程自旋。
###### 自旋状态
线程会在以下两种情形下进入自旋的状态。
  * 进入阻塞状态之前
  * 被唤醒后得不到锁的时候

##### 轻量级锁

##### 偏向锁
