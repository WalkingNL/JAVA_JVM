## synchronized同步锁的实现
java中的synchronized关键字用来处理java中多线程同步的问题，既可以用它声明一个被synchronized关键字标注的代码块，也可以用它直接标注静态方法或实例方法。具体对于该关键字如何使用，我们另行讨论。这篇文章的目的主要介绍一下该关键字是如何实现的。

#### 两个指令
##### monitorenter 与 monitorexit
首先对如下的代码进行编译，然后对生成的.class文件，执行指令`javap -verbose Test.class`，编译生成如下图所示的字节码。

    public class Test {
      public synchronized void test(Object lock) {
            lock.hashCode();
        }
    }
![](https://github.com/WalkingNL/Pics/blob/master/synchronized.jpg)
