# 递归

## 递归的定义

**递归是一种经典的系统压栈场景，传递参数的行为称之为递，返回结果的过程称为归。递归一般就是程序调用自身**

## 满足递归设计的条件

1. 一个问题可以分解为几个问题
2. 这个问题与分解的子问题除了数据规模不一样，求解思路一样
3. 存在递归中止条件

## 如何写出递归代码

**设计递归公式，找到中止条件**

1. 例如斐波那契数列。1,1,2,3,5,8,13,21,34.......大体满足f(n)=f(n-1)+f(n-2)。这个这就是递归的公式，然后中止条件是不满足这个公式的情况，例如 f(1)=f(0)+f(-1)但是f(0)跟f(-1)并不存在，所以要想这个公式成立，必须满足n>2。所以中止条件是f(1)=1,f(2)=1。例如一道经典的题目：求第n位斐波那契数列位置的值

   ```java
   public int fibonacci(int n){
     if(n<=0){
       throw new RunTimeException() ;
     }
     if(n==1||n=2){
       return 1;
     }
     return fibonacci(n-1)+fibonacci(n-2);
   }
   ```

2. 例如有 n 个台阶，每次你可以跨 1 个台阶或者 2 个台阶，请问走这 n 个台阶有多少种走法？如果有 7 个台阶，你可以 2，2，2，1 这样子上去，也可以 1，2，1，1，2 这样子上去。这个问题怎么分解呢。题目是上台阶，我们可以反过来理解为下台阶，我们在第n层台阶上往下走，因为一次只能走一步或者两步，所以所有的走法可以分为两类，第一次下一个台阶的，第一次下两个台阶的，那么下了一个台阶后的走法是不是又可以分解为之前一样的步骤呢?下了两个台阶之后的走法是不是也可以分解为之前一样的走法呢？所以也可以的到这么一个公式，f(n)=f(n-1)+f(n-2)。同样的需要判断中止条件n需要大于2，那么n在等于1的时候是不是只有一种走法即f(1)=1,n=2的时候有两种走法 1 1或者2那么f(2)=2,那么f(3)=f(2)+f(1)=3

   ```java
   public void taijie(int n){
    if(n<=0){
       throw new RunTimeException() ;
     }
     if(n==1){
       return 1;
     }
     if(n==2){
       return 2;
     }
     return taijie(n-1)+taijie(n-2);
   }
   ```

3. 再例如我们当时的单链表反转，我们的思路是使用一个循环，创建两个空节点prev和next，将这个next指向当前节点的后继节点，并将当前节点的后继节点改为prev，然后prev又指向为当前节点，node=next。集体代码如下

   ```java
   public Node reverseNode(Node node){
     Node prev,next=null;
     while(node!=null){
       next=node.next;
       node.next=prev;
       prev=node;
       node=next;
     }
     return prev;
   }
   ```

   那么这个循环是不是可以改写成递归呢，原来的顺序是 f(n).next=f(n+1),我们现在需要反转链表，将f(n).next=f(n-1),代码如下

   ```java
   public Node reverseNode(Node node){
     if(node.next==null){
       return node;
     }
     Node temp=node.next;
     Node newHead=reverseNode(node.next);
     temp.next=node;
     node.next=null;
     return next;   
   }
   ```

   

## 递归的隐患

1. 堆栈溢出：由于递归是系统帮忙压栈，当递归深度到达一定层次，会导致堆栈溢出

2. 重复计算：比如斐波那契数列，求f(5)=f(4)+f(3),而f(4)=f(3)+f(2),f(3)求了两次，可以使用hash将已计算的结果存入hashmap，避免重复计算

   ```java
   public int f(int n) {
     if (n == 1) return 1;
     if (n == 2) return 2;
     
     // hasSolvedList可以理解成一个Map，key是n，value是f(n)
     if (hasSolvedList.containsKey(n)) {
       return hasSolvedList.get(n);
     }
     
     int ret = f(n-1) + f(n-2);
     hasSolvedList.put(n, ret);
     return ret;
   }
   ```

## 将递归代码改为非递归代码

**递归代码虽然简洁表现力强，但是其空间复杂度高，可能有重复计算，过多的函数调用会耗时较多，有时候我们需要将递归代码改为非递归代码**

```java
public int taijie(int n){
  if(n<=0){
    throw new RunTimeException() ;
  }
  if(n=1){
    return 1;
  }
  if(n=2){
    return 2;
  }
  int res=0;
  int first=1;
  int second=2;
  for(int i=3,i<=n,i++){
    res=first+second;
    first=second;
    second=res;
  }
  return res;
}
```

