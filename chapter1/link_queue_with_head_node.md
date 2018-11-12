# 带头结点的队列

> ## 概念

看过我前面文章的同学应该知道，另外写有一篇《[不带头结点的队列](http://lircs.com/271.html)》,可想而知，这里所说的带头结点刚才那篇中不带头结点相对来说的。因为整个队列的抽象(ADT)都一样，只是实现的时候有没有头结点而已。

让我们来回忆一下，不带头结点的队列，它需要一个头指针(front)一个尾指针(rear)，分别用来指向第一个结点和最后一个结点。而每个结点又是由一个指针域(next)和数据域(data)组成，这样我们就可以使用这几个指针来找到队列中的每一个数据了。具体可以如下实现：

```c
typedef int data_t;

typedef struct __link_node {
    data_t data;
    struct __link_node *next;
} link_node;

typedef struct __link_queue{
    link_node *front;
    link_node *rear;
} link_queue;
```

> ## 队列的相关实现

还是一样，先来看图，从视觉上理解队列。

![带头结点的队列](http://on-img.com/chart_image/5b9df77ae4b015327ae21c7e.png?_=1537401555724)

如果想要知道和不带头结点队列的差异，可以查看文章《[不带头结点的队列](http://lircs.com/271.html)》。

那么可以看出，队列的头指针还是直接指向第一个结点的，而是指向头结点。再通过头结点去寻找第一个结点，这个头结点可以看作是只有指针域的结点，它的数据域可以不用，或者把数据域用来保存一些东西也是可以的，像：队列的长度等等，要灵活运用。

接下来，我们按照图来实现一个空队列，因为这种实现中，它有一个特殊的结点——头结点。那么在队列的实现的时候对于这个头结点也要做特殊的处理。

- 队列初始化

```c
//初始化队列
int init_queue(link_queue *q)
{
    // 1. 产生头结点，然后让头指针和尾指针都指向头结点
    q->front = q->rear = (link_node *)malloc(sizeof(link_node));
    if(q->front == NULL) {
        printf("init the queue error\n");
        return -1;
    }
    //2. 置空头结点的指针域
    q->front->next = NULL;

    return 0;
}
```

- 入队

和之前一样，我们先画图来弄清楚入队和过程，再实现这个过程。

![入队](http://on-img.com/chart_image/5b9df7b5e4b08faf8c5677a9.png?_=1537402304817)

由图可知，这个入队的过程也非常的简单，只需要新申请一个结点，然后把原来的尾结点的指针域指向新的尾结点(不然以后就找不到了)，然后再尾尾指针也指向新的新的尾结点。

```c
//入队
int enqueue(link_queue *q, data_t e)
{
    //1. 申请一个新的结点,也就是产生一个新的队列元素
    link_node *pnew = (link_node *)malloc(sizeof(link_node));
    if(pnew == NULL) {
        printf("malloc the new node error\n");
        return -1;
    }

    //2. 给新结点的数据域赋值，并把新结点的指针域置为空
    pnew->data = e;
    pnew->next = NULL;

    //3. 把前任的队尾元素的指针域指向新元素，即把新结点链接到队列的队尾
    q->rear->next = pnew;
    //4. 更新队列元素，把队尾元素指向新的队尾元素
    q->rear = pnew;

    return 0;
}
```

- 出队

![出队](http://on-img.com/chart_image/5b9dfa4ce4b0d4d65c0848d8.png?_=1537402518000)

整个过程是，先申请一个临时结点用来保存原来的第一个结点(A1)，然后把头结点的指针域(next)指向原第二个结点，也就量原第二结点已经上位(哈哈)。另外需要考虑的是，如果队列中只有一个结点，即出队以后队列变成了空队列，那么在出队以后，要把尾指针指向头结点来置空队列。

```c
//出队
int dequeue(link_queue *q, data_t *e)
{
    //1 建立一个临时结点用来操作
    link_node *pdel;
    if(q->front == q->rear) {
        printf("the queue is empty\n");
        return -1;
    }

    //2 将要出队的点的指针赋值给临时结点
    pdel = q->front->next;

    //3 把要出队的的点数据带出去
    *e = pdel->data;

    //4 更新队列第一结点(把原来的第二结点(原第一节点的后继)赋值给头结点的后继)
    q->front->next = pdel->next;

    //5 如果要出队的点已经是队尾的话，将队尾指针指向头结点，即置空队列
    if(q->rear == pdel)
        q->rear = q->front;

    //6 释放原第一结点的指针    
    free(pdel);

    return 0;
}
```

- 补全其它的操作

下面的几个操作比较简单，直接看代码：

```c
/返回队头元素
int get_head(link_queue *q, data_t *e)
{
    link_node *p;

    //如果队列不为空，则返回第一元素
    if (q->rear == q->front) 
        return -1;
    p = q->front->next; //第一元素即为头结点的后继
    *e = p->data;

    return 0;
}

//求队列的长度
int count_queue_len(link_queue *q)
{
    //return q->items;
    int count;

    //用一个工作指针遍历整个队列
    //traversals
    link_node *ptrav;

    ptrav = q->front;
    while(ptrav->next != ptrav) {
        count++;
        ptrav = ptrav->next;
    }
    return count;
}

//判断队列是否为空
bool is_queue_empty(link_queue *q)
{
    //判断队头指针和队尾指针是否重合，也就是是否都指向头结点
    if(q->rear == q->front)
        return true;
    else
        return false;
}

//清空队列
int clear_queue(link_queue *mq)
{
    link_node *p;
    link_node *q;

    //1 把队列Q的尾指针指向头指针
    mq->rear = mq->front;

    //2 用工作指针p保存第一结点的指针，即头结点的后继
    p = mq->front->next;

    //3 把头结点的后继置为空
    mq->front->next = NULL;

    //4 循环释放各个结点
    while(p) {
        q = p;
        p = p->next;
        free(q);
    }
    return 0;
}

//销毁队列
bool destroy_queue(link_queue *q)
{
    //从头结点开始循环删除所有的结点，包括头结点
    while(q->front) {
        q->rear = q->front->next;
        free(q->front);
        q->front = q->rear;
    }
    return true;
}

```

- 测试

用下面的代码对每个接口进行简单的测试。

```c
/#define NO_HEAD_NODE
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
过此为止呢，队列的一些常用的操作我们都基本实现了，不过记得要学回来看看哟，这样方才会在成为大神的路上越走越远。

> ## 相关阅读

[[数据结构] 队列的原理和认识](http://lircs.com/268.html)   
[[数据结构] 不带头结点的队列](http://lircs.com/271.html)