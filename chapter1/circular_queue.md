# 循环队列

> ## 概念

想要了解一个概念，首先要对概念有一个理性的认识。比如循环队列，为什么叫循环队列，前面的文章中，我们一直说队列是站队，是排队。那么循环队列和前面所说的队列有什么不一样呢？循环队列是为了解决什么问题而存在的呢？说到这里，大家是不是也和我一样有疑问呢？

好，那我们就先来看看循环队列和之前说的队列有什么关系。如下图：

![循环队列](http://on-img.com/chart_image/5ba42896e4b0fe81b640c151.png?_=1537488325953)

如图所示，循环队列是为了解决队列的假溢出问题，把队列的首先连起来，使队列在逻辑上成为了一个环形，所以叫循环队列。

> ## 循环队列的实现

实现循环，通常使用数组来实现，另外有一点需要注意的是，循环队列的空和満的处理。为什么这么说呢，队列和实现通常都有一个头指针和一个尾指针，对于循环队列来说也一样，但是，循环队列空和満时，头尾指针都重合在一起，这就需要我们对循环队列的头尾指针要做特殊对待了。

1. 可以对队列元素个数进行计数，那么，我们就可以判断，当元素个数不等于0,且头尾指针重合时队列为満。当元素个数为0时队列为空。   
2. 预留一个元素空间不用，那么，当尾指针+1等于头指针时就可以判断队列为満。

说了这么多，那循环队列到底是该怎么抽象呢？往下看

```c
#define MAX_Q_SIZE 6

typedef int data_t;

typedef struct {
    data_t data[MAX_Q_SIZE];
    int rear;
    int front;
    int count;
}circular_queue;
```
使用数组实现的循环队列，添加一个计数器(count),方便判断队列満的情况。

下面是循环队列的具体实现：

```c
/* 初始化 */
int init_queue(circular_queue *q)
{
    //初始化队列的相关参数
    q->front = 0;
    q->rear = 0;
    q->count = 0;
    
    //因为是数组实现，所以，我们需要对元素数组进行初始化
    memset(q->data, 0, sizeof(q->data));

    return 0;
}

/* 入队 */
int enqueue(circular_queue *q, data_t e)
{
    //判断为満，直接返回，无法入队
    if(q->count > 0 && q->rear == q->front) {
        printf("%s: the queue is full, can not enqueue\n", __func__);
        return -1;
    }

    //给新结点的数据域赋值
    q->data[q->rear] = e;
    
    //移动队尾指针
    q->rear = (q->rear + 1) % MAX_Q_SIZE;   //这个操作是实现循环队列的关键，一定要解理了
    
    //计数器+1
    q->count++;

    return 0;
}

/* 出队 */
int dequeue(circular_queue *q, data_t *e)
{
    //判断队列为空，直接返回，无法出队
    if(q->count == 0) {
        printf("%s: the queue is empty, can not dequeue\n", __func__);
        return -1;
    }

    //带出要出队结点的值域。
    *e = q->data[q->front];
    
    //移动对头指针
    q->front = (q->front + 1) % MAX_Q_SIZE;
    
    //计数器-1
    q->count--;

    return 0;
}

/* 读取队头元素 */
int get_head(circular_queue *q, data_t *e)
{
    //判断队列为空，直接返回，没有元素可以获取
    if(q->count == 0) {
        printf("%s: the queue is empty, can not dequeue\n", __func__);
        return -1;
    }

    //带出队头元素的值域
    *e = q->data[q->front];
    return 0;
}

/* 求队列长度 */
int count_queue_len(circular_queue *q)
{
    //返回队列的长度
    return q->count;
}

/* 判断队列为空 */
bool is_queue_empty(circular_queue *q)
{
    //通过计数器判断队列为空？
    return q->count == 0 ? true : false;
}

/* 销毁队列 */
//静态数据的内存不需要释放，只需要清除相关的数据即可
bool destroy_queue(circular_queue *q)
{
    if(q->count == 0) {
        printf("%s: the queue is empty, no need to destory\n", __func__);
        return false;
    }

    q->front = q->rear = q->count = 0;
    memset(q->data, 0, sizeof(q->data));

    return true;
}

```

测试程序：

```c
//#define NO_HEAD_NODE
// #define WITH_HEAD_NODE
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

> ## 相关阅读

[[数据结构] 队列的原理和认识](http://lircs.com/268.html)   
[[数据结构] 不带头结点的队列](http://lircs.com/271.html)   
[[数据结构] 带头结点的队列](http://lircs.com/272.html)   