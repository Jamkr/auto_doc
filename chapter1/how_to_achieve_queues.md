# 队列的原理和认识

> ## 队列的基本概念

队列(queue)是一种特殊的线性表，它们的差别在于，链表则可以在任意位置进行插入和删除元素，队列只允许在一端进行插入操作，而在另一端进行删除操作。进行插入操作的端称之为队尾(rear/tail)，进行删除操作的一端称为队头(front/head)，队列中没有元素时称为空队列。

队列的数据元素以称为队列元素，在队列中插入一个队列元素称为入队(equeue)，从队列中删除一个队列元素称为出队(dequeue)。又因为它只允许在一端插入而在另一端删除，所以队列中的元素是先进入的元素也是最先从队列中删除，所以又称为先进先出(FIFO)线性表。

> ## 认识队列

上面说的都是一些书面的概念，可能还不是很清楚，因为书本上对于这些概念的说明大都是这样的，看完了你觉得明白了，然而细想想，又好像没明白(呵呵)，我在这里用一种更加通俗易懂的方式来说明队列这个概念。

好，那么说到队列，你最先想到的是什么呢，没错，是“队伍” “排队” “插队”这些词语，是吧。那么这些词语描述是不是让你想到了排队 站队，想想，“一二一 一二一”，是不是（哈哈）。请接着往下看，我们用图的来给大家画一下队列是什么样的？队列到底是怎么排队的？

![队列的认识](http://on-img.com/chart_image/5b9f07bbe4b0bd4db93ef89d.png?_=1537151335604)

看完这个图应该对队列有一个整体的认识了。认识了队列，我们就来认识下队列的分类，从实现方式上可以分为顺序队列和链队列，后面，我也会对下面的几种队列进行分别的介绍。

![队列的分类](http://on-img.com/chart_image/5b9e54b6e4b0534c9bddb081.png?_=1537108287900)

> ## 队列的抽象

1. 队列的初始化
2. 入队
3. 出队
4. 求队列长度
5. 遍历队列
6. 判断队列是否为空
7. 判断队列是否为満
8. 读取队头元素
9. 删除队头元素
10. 销毁队列

队列的基本的操作就这些了，当然最基本的初始化，入队，出队更是必须要掌握的。

在接下来的文章当中，我们会把这些抽象分别实现一下，让大家有一个更加深刻的认识。

> 相关阅读

[数据结构和算法分类](http://lircs.com/255.html)
