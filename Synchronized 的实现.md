## synchronized同步锁的实现
java中的synchronized关键字用来处理java中多线程同步的问题，既可以用它声明一个被synchronized关键字标注的代码块，也可以用它直接标注静态方法或实例方法。具体对于该关键字如何使用，我们另行讨论。这篇文章的目的主要介绍一下该关键字是如何实现的。

#### 两个指令
##### monitorenter 与 monitorexit
首先编译下面的java代码，然后对生成的.class文件，执行指令`javap -verbose Test.class`。产生如下图所示的字节码，能够看到一个`monitorenter`对应两个`monitorexit`指令。这里的两个monitorexit指令，是为了保证在任何状况下，锁都能够正常退出。所以实际上`monitorenter`与`monitorexit`是一对一的关系。这两个指令各自会消耗操作数栈上的一个引用类型的元素，作为加解锁的锁对象。也就是下面代码中的lock。

    public class Test {
      public void test(Object lock) {
            synchronized (lock) {
                lock.hashCode();
            }
        }
    }
![](https://github.com/WalkingNL/Pics/blob/master/synchronized.jpg)
