# AQS

AQS ： AbstractQueuedSynchronizer

AbstractQueuedSynchronizer又称为队列同步器\(后面简称AQS\)，它是用来构建锁或其他同步组件的基础框架，内部通过一个int类型的成员变量state来控制同步状态,当state=0时，则说明没有任何线程占有共享资源的锁，当state=1时，则说明有线程目前正在使用共享变量，其他线程必须加入同步队列进行等待，AQS内部通过内部类Node构成FIFO的同步队列来完成线程获取锁的排队工作，同时利用内部类ConditionObject构建等待队列，当Condition调用wait\(\)方法后，线程将会加入等待队列中，而当Condition调用signal\(\)方法后，线程将从等待队列转移动同步队列中进行锁竞争。注意这里涉及到两种队列，一种的同步队列，当线程请求锁而等待的后将加入同步队列等待，而另一种则是等待队列\(可有多个\)，通过Condition调用await\(\)方法释放锁后，将加入等待队列。关于Condition的等待队列我们后面再分析，这里我们先来看看AQS中的同步队列模型，如下

```text
/**
 * AQS抽象类
 */
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer{
//指向同步队列队头
private transient volatile Node head;

//指向同步的队尾
private transient volatile Node tail;

//同步状态，0代表锁未被占用，1代表锁已被占用
private volatile int state;

//省略其他代码......
}
```

![](../../.gitbook/assets/image%20%2824%29.png)



AQS同步器的实现依赖于内部的同步队列\(FIFO的双向链表对列\)完成对同步状态\(state\)的管理，当前线程获取锁\(同步状态\)**失败时，AQS会将该线程以及相关等待信息包装成一个节点\(Node\)并将其加入同步队列，同时会阻塞当前线程**，当同步状态释放时，会将头结点head中的线程唤醒，让其尝试获取同步状态。关于同步队列和Node结点，前面我们已进行了较为详细的分析，这里重点分析一下获取同步状态和释放同步状态以及如何加入队列的具体操作，这里从ReetrantLock入手分析AQS的具体实现，我们先以非公平锁为例进行分析。



公平锁与非公平锁

该方法与`nonfairTryAcquire(int acquires)`方法唯一的不同是在使用CAS设置尝试设置state值前，调用了`hasQueuedPredecessors()`判断同步队列是否存在结点，如果存在必须先执行完同步队列中结点的线程，当前线程进入等待状态。这就是非公平锁与公平锁最大的区别，即公平锁在线程请求到来时先会判断同步队列是否存在结点，如果存在先执行同步队列中的结点线程，当前线程将封装成node加入同步队列等待。而非公平锁呢，当线程请求到来时，不管同步队列是否存在线程结点，直接尝试获取同步状态，获取成功直接访问共享资源，但请注意在绝大多数情况下，非公平锁才是我们理想的选择，毕竟从效率上来说非公平锁总是胜于公平锁。   


```text
/**
 * Sync object for fair locks
 */
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        acquire(1);
    }

    /**
     * Fair version of tryAcquire.  Don't grant access unless
     * recursive call or no waiters or is first.
     */
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            //公平锁会去判断同步队列是否有结点
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```

 

### Condition的实现原理 <a id="condition&#x7684;&#x5B9E;&#x73B0;&#x539F;&#x7406;"></a>

Condition的具体实现类是AQS的内部类ConditionObject，前面我们分析过AQS中存在两种队列，一种是同步队列，一种是等待队列，而等待队列就相对于Condition而言的。注意在使用Condition前必须获得锁，同时在Condition的等待队列上的结点与前面同步队列的结点是同一个类即Node，其结点的waitStatus的值为CONDITION。在实现类ConditionObject中有两个结点分别是firstWaiter和lastWaiter，firstWaiter代表等待队列第一个等待结点，lastWaiter代表等待队列最后一个等待结点，如下

```text
public class ConditionObject implements Condition, java.io.Serializable {
    //等待队列第一个等待结点
    private transient Node firstWaiter;
    //等待队列最后一个等待结点
    private transient Node lastWaiter;
    //省略其他代码.......
}
```

