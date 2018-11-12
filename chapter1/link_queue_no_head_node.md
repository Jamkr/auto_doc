# 不带头结点的队列

### 概念

在[队列的原理和认识](http://lircs.com/268.html)这篇文章中，我们说到，队列分为顺序队列和链队列。它们是分别使用数组和链表实现的。而链队又分为带头结点的链队和不带头结点的链队。这篇文章中，我们先来说一下，不带头结点链队的实现。

### 队列ADT

我们知道，队列是由元素一个一个链在一起的，为了能够很好的入队，出队，构建队列时，包含一个头指针(front)和一个尾指针(rear)。而其中每个元素中又包含一个数据域(data)和一个指针域(next)，数据域用来保存元素中的数据，指针域用来链接当前结点元素的后继元素。所以队列可以抽象为：

```c
/** 定义数据域 **/
typedef int data_t;

/** 定义节点 **/
typedef struct __link_node {
    data_t data;    //数据域
    struct __link_node *next;   //指针域
} link_node;

/** 定义队列 **/
typedef struct __link_queue{
    link_node *front;   //头指针，用来指向头结点
    link_node *rear;    //尾指针，用来指向尾结点
} link_queue;
```

队列的操作有：

1. 队列初始化(`int init_queue(link_queue *q);`)
2. 入队(`int equeue(link_queue *q, data_t e);`)
3. 出队(`int dequeue(link_queue *q, data_t *e);`)
4. 获取头结点 (`int get_head(link_queue *q, data_t e);`)
5. 计算队列长度 (`int count_queue_len(link_queue *q);`)
6. 判断队列为空 (`bool is_queue_empty(link_queue *q);`)
7. 判断队列为満 (`bool is_quque_full(link_queue *q);`)
8. 销毁队列 (`bool destroy_queue(link_queue *q);`)

### 队列的相关实现

先来看看队列的形式。

![正常队列](http://on-img.com/chart_image/5b9dcb06e4b0fe81b6381287.png?_=1537313199228)

从上面的几个图中，我们可以很好的看出，队列的组成形式，把图画出来，我们就可以更好的来理解队列，并且可以理顺逻辑，所以对于编程来说，画架构图，流程图是非常重要的。也是一个很好的习惯。下面就根据这些图来开始编码实现一个简单的队列。

```c
/* 队列的初始化 */
int init_queue(link_queue *q)
{
    //初始化一个空队列，即把头指针和尾指针都指向NULL
    q->front = q->rear = NULL;  
    return 0;
}
```

这样写是不是很简单，下面再看下入队和出列是怎样操作的。

![入队](http://on-img.com/chart_image/5b9b3e64e4b0fe81b635deb4.png?_=1537313890921)

从图中可以看出，入队的时候对于第一个结点入队时需要特殊的红对待以外，其它的结点，只需要移动尾指针(rear)就行了。

```c
/* 入队 */
int enqueue(link_queue *q, data_t e)
{
    //1. 申请一个结点
    link_node *pnew = (link_node *)malloc(sizeof(link_node));
    if(!pnew) {
        printf("%s: malloc fail\n", __func__);
        return -1;
    }

    //给新结点的数据域赋值，并把新结点的指针域置为空
    pnew->data = e;
    pnew->next = NULL;
    
    if(q->front == NULL) { //入队的如果是第一个结点，则把头指针和尾指针都指向新结点
        q->front = q->rear = pnew;
    } else{ //其它的则先把尾结点的链接到新结点，再链表尾指针指向新结点
        q->rear->next = pnew;
        q->rear = pnew;
    }
    return 0;
}

```

![出队](http://on-img.com/chart_image/5b9dcc2ce4b0d4d65c0821af.png?_=1537313925738)

接着，我们根据队列来实现队列出队，而对于只有一个结点的队列，我们也需要做特殊的处理，想想该怎么处理呢？

```c
/* 出队 */
int dequeue(link_queue *q, data_t *e)
{
    //声明一个临时指针
    link_node *pdel;

    //队列为空则直接退出，无法完成出队
    if(q->front == NULL) {
        printf("%s: the queue is empty +++\n", __func__);
        return -1;
    }

    //把队列头结点赋值给临时指针
    pdel = q->front;
    *e = pdel->data;

    //从原队列中去掉头结点
    if(q->front == q->rear) {   //如果原队列中只有一个结点，去掉以后把头指针和尾指针都置为空。
        q->front = q->rear = NULL;  
    } else {    //把头指针的指向原头结点的后继，即指向原队列第二结点
        q->front = pdel->next;
    }

    //释放原头结点的空间
    free(pdel);

    return 0;
}
```

这样我们就完成队列中两个重要的操作入队(`enqueue`)和出队(`dequeue`)。下面把另外几个操作也补上。并且在最后会写一个测试程序来测试我们写的队列

```c
/* 获取头结点 */
int get_head(link_queue *q, data_t *e){
    //空队列直接返回
    if(q->front == NULL) {
        printf("%s: the queue is empty\n", __func__);
        return -1;
    }

    //返回头结点的指针
    *e = q->front->data;

    return 0;
}

/* 计算队列的长度 */
int count_queue_len(link_queue *q)
{
    //声明一个临时指针用来遍历队列
    link_node *ptrav;
    int count;

    //队列为空直接返回
    if(q->front == NULL) {
        printf("%s: the queue is empty\n", __func__);
        return -1;
    }

    //把临时指针指向头结点
    ptrav = q->front;

    //遍历队列元素
    while(ptrav) {
        count++;
        q->front = ptrav->next;
        ptrav = q->front;
    }

    return count;
}

/* 判断队列为空 */
bool is_queue_empty(link_queue *q)
{
    if(q->front == NULL)
        return true;
    else
        return false;
}

```

下面写一段程序来测试上面的代码

```c
//#define NO_HEAD_NODE
//#define WITH_HEAD_NODE
#define CIRCULAR_Q

#ifdef NO_HEAD_NODE
    #include "queue_no_head_node.h"
#endif

#ifdef WITH_HEAD_NODE
    #include "queue_with_head_node.h"
#endif

#ifdef CIRCULAR_Q
    #include "circular_queue.h"
#endif

#include "stdio.h"


#ifndef CIRCULAR_Q
void print_queue(link_queue *q)
{
    link_node *ptrav;
    if(q->front == NULL) {
        printf("%s: it is a empty link queue\n", __func__);
        return;
    }
    ptrav = q->front;
    printf("%s: elem ", __func__);
    while(ptrav) {
        printf("[%d] ", ptrav->data);
        ptrav = ptrav->next;;
    }
    printf("\n");
    return;
}
#else
void print_queue(circular_queue *q)
{
    if(q->count == 0) {
        printf("%s: it is a empty circular queue\n", __func__);
        return;
    }
    printf("%s: elem ", __func__);
    for(int i = 0; ((i+1) % MAX_Q_SIZE) != q->rear; i++) {
        printf("[%d] ", q->data[i]);
    }

    printf("\n");
    return;
}
#endif

int main(void)
{
#ifdef NO_HEAD_NODE
    printf("%s: Start no head node queue test\n", __func__);
#elif  WITH_HEAD_NODE
    printf("%s: Start has head node queue test\n", __func__);
#else
    printf("%s: Start circular queue test\n", __func__);
#endif

#ifdef CIRCULAR_Q
    circular_queue test_queue;
#else
    link_queue test_queue;
#endif
    int elem, i;
    int j = 0;

    /* init */
    init_queue(&test_queue);

    printf("after init, the queue is %s empty\n", is_queue_empty(&test_queue) ? "" :"not");

    /* equeue */
    data_t array[] = {12, 23 , 5, 9, 17};
    for(i = 0; i < 5; i++) {
        printf("%s: equeue [%d]\n", __func__,  array[i]);
        enqueue(&test_queue, array[i]);
        print_queue(&test_queue);
    }

    /* deqeue */
    for(i = 0; i < 4; i++) {
        dequeue(&test_queue, &elem);
        printf("%s: dequeue [%d] elem is[%d] \n", __func__, ++j, elem);

        enqueue(&test_queue, i+30);
    }

    //after dequeue some elements
    print_queue(&test_queue);

    /* destory queue */
    printf("%s: destory the queue\n", __func__);
    destroy_queue(&test_queue);
    printf("after destory, the queue is %s empty\n", is_queue_empty(&test_queue) ? "" :"not");

    return 0;
}
```

### 相关阅读

[队列的原理和认识](http://lircs.com/268.html)
