---
layout: post
title: 几种常见排序算法的学习
description: "sort algorithm"
tags: [algorithm]
image:
  background: ps_neutral.png
---


> 排序算法是面试中常被提及的算法种类之一，以下记录了我学排序的一些想法。

###插入排序

####介绍

插入排序(insertion sort)是最简单的排序算法之一。插入排序由 `N - 1` 趟(pass)排序组成。对于`P = 1` 趟到 `P = N - 1` 趟，插入算法保证从位置 0 到位置 P 上的元素为已排序状态。

{% highlight c %}
void InsertionSort(ElementType A[],int N)
{
    int j, P;
    
    ElementType Tmp;
    for(P = 1; P < N; P++)
    {
        Tmp = A[P];
        for(j = P; j > 0 && A[j-1] > Tmp; j--)
            A[j] = A[j-1];
        A[j] = Tmp;
    }
}

{% endhighlight %}

####插入排序的分析

嵌套循环的每一个都花费 `N` 次迭代,
对所有的P进行求和，得到总数为
![](http://latex.codecogs.com/gif.latex?%5Csum_%7Bi%3D2%7D%5E%7BN%7Di%3D2&plus;3&plus;4&plus;...&plus;N= \Theta(N^{2}\))

因此插入排序为![](http://chart.googleapis.com/chart?cht=tx&chl=O(N^{2}\))。

####总结

其实就是从左到右，取每个元素，通过比较让其放在正确的位置。相比同样是![](http://www.forkosh.com/mathtex.cgi?%20O(N^{2}\))的冒泡排序，插入排序减少了交换元素的次数。

###希尔排序

####介绍

希尔排序(Shellsort)，有时也叫缩小增量排序(deminishing increment sort)，通过比较相距一定间隔的元素来工作；各趟比较所用的距离随着算法的进行而减小，直到只比较相距相邻元素的最后一趟排序为止。

增量的选择有多种，不同的增量，性能也不同。较为常见的是使用希尔增量。以下是以增量为`N / 2`的算法。

{% highlight c %}
void ShellSort(ElementType A[], int N)
{
    int i,j,Increment;
    ElementType Tmp;
    
    for (Increment = N / 2; Increment > 0; Increment /= 2) 
    {
        for (i = Increment; i < N; i++) 
        {
            Tmp = A[i];
            for(j = i; j >= Increment; j-= Increment)
                if (Tmp < A[j - Increment])
                    A[j] = A[j - Increment];
                else
                    break;
            A[j] = Tmp;
        }
    }
}

{% endhighlight %}

####总结

希尔排序的性能取决于增量的选择。希尔排序说白了就是先使相同间隔的元素有序，然后缩写间隔，从而完成整个排序。

###堆排序

####介绍

堆排序(heapsort)，基于优先队列可以用花费![](http://chart.googleapis.com/chart?cht=tx&chl=O({N}\log{N}\))时间的排序的特性。然而实践中它却慢于使用Sedgewick增量序列的希尔排序。

建立堆花费的时间是![](http://chart.googleapis.com/chart?cht=tx&chl=O(N\))，然后执行`N`次`DeleteMin`操作。按照顺序，最小的元素先离开该堆。每次`DeleteMin`花费时间![](http://chart.googleapis.com/chart?cht=tx&chl=O(\log{N}\)),因此总的运行时间是![](http://chart.googleapis.com/chart?cht=tx&chl=O({N}\log{N}\)).

该算法的主要问题在于它使用了一个附加数组。因此，存储需求增加一倍。在每次`DeleteMin`之后，堆缩小1，将堆中最后一个单元用来存放刚刚删去的元素，即可避免使用第二个数组。

####算法分析

堆排序的过程是先建堆，后操作。

#####建堆

将数组视为一颗完全二叉树，从其最后一个非叶子节点`n / 2`开始调整。将该节点的儿子节点中，较大的节点与该节点互换。
![](http://images.cnitblog.com/blog/305504/201301/23175544-9a438436abaa4043a5f00191fbc4626b.png)

#####DeleteMax

将堆中最后一个元素与第一个元素交换，缩减堆的大小并进行下滤，来执行`N - 1`次DeleteMax操作。

####总结

堆排序的总结起来就两步，建堆的部分比较重要，需要慢慢理解才能弄懂。

{% highlight c %}
void PercDown(ElementType A[],int i,int N)
{
    int Child;
    ElementType Tmp;

    for(Tmp = A[i];LeftChild(i) < N;i = Child)    
    {
        Child = LeftChild(i);                            // 以下修改可实现从大到小排序
        if(Child != N - 1 && A[Child + 1] > A[Child])    // A[Child + 1] < A[Child]
            Child++;
        if(Tmp < A[Child])                               // Tmp > A[Child]
            A[i] = A[Child];
        else
            break;
    }
    A[i] = Tmp;
}
void Heapsort(ElementType A[],int N)
{
    int i;

    for(i = N / 2;i >= 0;i--)   // BuildHeap
        PercDown(A,i,N);
    for(i = N - 1;i > 0;i--)    // DeletMax
    {
        swap(&A[0],&A[i]);
        PercDown(A,0,i);
    }
}
{% endhighlight %}

###快速排序

####介绍
快速排序(quicksort)是实践中最快的已知排序算法，它的平均运行时间是
![](http://chart.googleapis.com/chart?cht=tx&chl=O({N}\log{N}\))。它的最坏情形的性能为![](http://chart.googleapis.com/chart?cht=tx&chl=O(N^{2}\))。快速排序也是一种分治的递归算法。讲数组S排序的基本算法由下列简单的四步组成:

* 1: 如果S中元素个数是0或1，则返回。
* 2: 取S中任一元素v，称之为`枢纽元(pivot)`。
* 3: 将S - {v}(S中其余元素)分成两个不相交的集合:![](http://latex.codecogs.com/gif.latex?S_%7B_%7B1%7D%7D%3D%5C%7Bx%5Cepsilon%20S-%7Bv%7D%7Cx%5Cleqslant%20v%5C%7D)和![](http://latex.codecogs.com/gif.latex?S_%7B_%7B2%7D%7D%3D%5C%7Bx%5Cepsilon%20S-%7Bv%7D%7Cx%5Cgeqslant%20v%5C%7D)。
* 4: 返回{quicksort(![](http://latex.codecogs.com/gif.latex?S_%7B_%7B1%7D%7D))后，继随v,继而quicksort(![](http://latex.codecogs.com/gif.latex?S_%7B_%7B2%7D%7D))}。


(To be continued)