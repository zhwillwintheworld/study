# 队列

## 队列定义

**队列和栈一样，都是操作受限的线性表，如同栈只有压栈和出栈两种操作一样，队列只有入队和出队两种操作，但与栈不同的是，栈是同一侧压栈出栈，而队列是一侧入一侧出，队列满足先进先出，后进后出的特性，真因为如此，我们可以使用队列这种结构来完成线程池的阻塞队列这种操作，以及高性能循环队列Disruptor**

## 队列的实现

1. **如同栈的实现一样，可以由数组完成顺序队列（0位置进入，后来者的index不断叠加，取的话从0的位置开始，并使用record属性来记录已经取到的index，当存储位置不够触发删除操作--将record后的元素按照顺序放到数组0-n之后的位置）**
2. **使用链表来完成一个链式队列，使用LinkedList这种结构最简单，从头部插入，尾部取（当插入第一个元素时tail==head，随后进来的node被指为head节点）**

## 循环队列

**有上述顺序队列实现引入思考，引入标记，到达条件触发删除操作并使用arraycopy迭代的将记录后面位置的数据搬运到index=0开始。这其实一个很消耗性能的操作（假设数组足够大）。因此引入思考，假设我们把数组做成一个圆，并使用一个空位置，并设置两个属性head，tail，以及数组的长度属性length。刚开始时，数组为空head=tail。当入队操作时tail+1。当出队操作时head+1。因此我们可以判断当head==tail队列为空。当tail==length时而head>0时，（我们引入思考，是否可以将tail放到0或者<head的位置，就这样一直周而复始，避免数组的数据搬移操作）事实证明这种操作时可以的，由于tail是空节点，我们不存储任何元素，所以只要(tail+1)%length!=head就可以保证队列未满。**

![img](https://static001.geekbang.org/resource/image/3d/ec/3d81a44f8c42b3ceee55605f9aeedcec.jpg)

**当（tail+1）%length==head就代表队列已满**

## 阻塞队列

**堵塞队列其实就是在队列基础上增加了阻塞操作。简单来说，就是在队列为空的时候，从队头取数据会被阻塞。因为此时还没有数据可取，直到队列中有了数据才能返回；如果队列已经满了，那么插入数据的操作就会被阻塞，直到队列中有空闲位置后再插入数据，然后再返回。**

## 并发队列

**并发队列其实就是保证线程安全，最简单直接的实现方式是直接在 enqueue()、dequeue() 方法上加锁，但是锁粒度大并发度会比较低，同一时刻仅允许一个存或者取操作。实际上，基于数组的循环队列，利用 CAS 原子操作，可以实现非常高效的并发队列。这也是循环队列比链式队列应用更加广泛的原因**

