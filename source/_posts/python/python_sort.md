---
title: python实现排序算法
date: 2019-07-05T10:05:19+08:00
toc: true
tags:
  - python
categories:
  - 技术文章
---

常见的八大排序算法，他们之间关系如下：
<!--more-->

![sort_1](/images/python/sort_1.jpg)

性能比较

![sort_2](/images/python/sort_2.jpg)

## 直接插入排序

![sort_3](/images/python/sort_3.gif)

直接插入排序的核心思想就是：将数组中的所有元素依次跟前面已经排好的元素相比较，如果选择的元素比已排序的元素小，则交换，直到全部元素都比较过。

因此，从上面的描述中我们可以发现，直接插入排序可以用两个循环完成：

1. 第一层循环：遍历待比较的所有数组元素

2. 第二层循环：将本轮选择的元素(selected)与已经排好序的元素(ordered)相比较。如果：selected > ordered，那么将二者交换

> 代码实现：

```python
def insert_sort(L):
    # 遍历数组中的所有元素，其中0号索引元素默认已排序，因此用1开始
    for x in range(1,len(L)):
    # 将该元素与已排序好的前序数组依次比较，如果该元素小，则交换
        for i in range(x-1,-1,-1):
            if L[i]>L[i+1]:
                temp=L[i+1]
                L[i+1]=L[i]
                L[i]=temp
```

## 希尔排序

![sort_4](/images/python/sort_4.png)

希尔排序的算法思想：将待排序数组按照步长gap进行分组，然后将每组的元素利用直接插入排序的方法进行排序；每次将gap折半减小，循环上述操作；当gap=1时，利用直接插入，完成排序。

同样的：从上面的描述中我们可以发现：希尔排序的总体实现应该由三个循环完成：

1. 第一层循环：将gap依次折半，对序列进行分组，直到gap=1
2. 第二、三层循环：也即直接插入排序所需要的两次循环。具体描述见上。

> 代码实现：

```python
def insert_shell(L):
    gap=(int)(len(L)/2)
    while gap>=1:
        for x in range(gap,len(L)):
            for i in range(x-gap,-1,-gap):
                if L[i]>L[i+gap]:
                    temp=L[i+gap]
                    L[i+gap]=L[i]
                    L[i]=temp
        gap=(int)(gap/2)
```

## 简单选择排序

![sort_5](/images/python/sort_5.gif)

简单选择排序的基本思想：比较+交换。

1. 从待排序序列中，找到关键字最小的元素；
2. 如果最小元素不是待排序序列的第一个元素，将其和第一个元素互换；
3. 从余下的 N - 1 个元素中，找出关键字最小的元素，重复(1)、(2)步，直到排序结束。

因此我们可以发现，简单选择排序也是通过两层循环实现。

第一层循环：依次遍历序列当中的每一个元素

第二层循环：将遍历得到的当前元素依次与余下的元素进行比较，符合最小元素的条件，则交换。

> 代码实现：

```python
def select_sort(L):
    for x in range(0,len(L)):
        mininum = L[x]
        for i in range(x+1,len(L)):
            if L[i]<mininum:
                temp=L[i]
                L[i]=mininum
                mininum=temp
        L[x]=mininum
```

## 堆排序

* 堆的概念：

堆：本质是一种数组对象。特别重要的一点性质：任意的叶子节点小于（或大于）它所有的父节点。对此，又分为大顶堆和小顶堆，大顶堆要求节点的元素都要大于其孩子，小顶堆要求节点元素都小于其左右孩子，两者对左右孩子的大小关系不做任何要求。

利用堆排序，就是基于大顶堆或者小顶堆的一种排序方法。下面，我们通过大顶堆来实现。

* 基本思想：

堆排序可以按照以下步骤来完成：

1. 首先将序列构建称为大顶堆（这样满足了大顶堆那条性质：位于根节点的元素一定是当前序列的最大值）；
![sort_6](/images/python/sort_6.png)
2. 取出当前大顶堆的根节点，将其与序列末尾元素进行交换（此时：序列末尾的元素为已排序的最大值；由于交换了元素，当前位于根节点的堆并不一定满足大顶堆的性质）；
3. 对交换后的n-1个序列元素进行调整，使其满足大顶堆的性质；
![sort_7](/images/python/sort_7.png)
4. 重复2.3步骤，直至堆中只有1个元素为止。

> 代码实现：

```python
# 获取左叶子节点
def LEFT(i):
    return 2*i+1
# 获取右叶子节点
def RIGHT(i):
    return 2*i+2
# 调整大顶堆
# L待调整序列， length 序列长度 i需要调整的节点
def adjust_max_heap(L,length,i):
    # 定义一个int值保存当前序列最大值的下标
    largest=i
    # 执行循环：
    # 1.寻找最大值的下标
    # 2.最大值与父节点交换
    while True:
        # 获取序列左右叶子节点的下标
        left=LEFT(i)
        right=RIGHT(i)
        # 当左叶子节点的下标小于序列长度并且右叶子节点的值大于父节点时，将左叶子节点的下标值赋值给largest
        if (left<length) and (L[left]>L[i]):
            largest=left
            print('左叶子节点')
        else:
            largest=i
        # 当右叶子节点的下标小于序列长度并且右叶子节点的值大于父节点时，将右叶子节点的下标值赋值给largest
        if (right<length) abd (L[right]>L[largest]):
            largest=right
            print('右叶子节点')
        # 如果largest不等于i，说明当前的父节点不是最大值，需要交换值
        if largest!= i:
            temp=L[i]:
            L[i]=L[largest]
            L[largest]=temp
            i=largest
            print(largest)
            continue
        else:
            break
# 建立大顶堆
def build_max_heap(L):
    length=len(L)
    for i in range((length-1)/2,-1,-1):
        adjust_max_heap(L,length,x)

# 堆排序
def heap_sort(L):
    # 先建立大顶堆，保证最大值位于根节点，并且父节点的值大于叶子节点
    build_max_heap(L)
    # 当前堆中序列的长度，初始化为序列的长度
    i=len(L)
    # 执行循环：
    # 1.每次取出堆顶元素置于序列的最后(len-1,len-2,len-3...)
    # 2.调整堆，使其继续满足大顶堆的性质，注意实时修改堆中序列的长度
    while i>0:
        temp=L[i-1]
        L[i-1]=L[0]
        L[0]=temp
        # 堆中序列长度减1
        i=i-1
        # 调整大顶堆
        adjust_max_heap(L,i,0)
```

## 冒泡排序

![sort_8](/images/python/sort_8.gif)

1. 将序列当中的左右元素，依次比较，保证右边的元素始终大于左边的元素（ 第一轮结束后，序列最后一个元素一定是当前序列的最大值）；
2. 对序列当中剩下的n-1个元素再次执行步骤1。
3. 对于长度为n的序列，一共需要执行n-1轮比较（利用while循环可以减少执行次数）。

> 代码实现

```python
def bubble_sort(L):
    length=len(L)
    # 序列长度为length，需要执行length-1轮交换
    for x in range(1,length):
    # 每一轮交换，都将序列当中的左右元素进行比较
    # 每轮交换中，由于序列最后的元素一定是最大的，因此每轮循环到序列未排序的位置即可
        for i in range(0,length-x):
            if L[i]>L[i+1]:
                temp=L[i]
                L[i]=L[i+1]
                L[i+1]=temp
```

## 快速排序

![sort_9](/images/python/sort_9.gif)

快速排序的基本思想：挖坑填数+分治法

1. 从序列当中选择一个基准数(pivot)，在这里我们选择序列当中第一个数最为基准数；
2. 将序列当中的所有数依次遍历，比基准数大的位于其右侧，比基准数小的位于其左侧；
3. 重复步骤1.2，直到所有子集当中只有一个元素为止。用伪代码描述如下：
    1. i =L; j = R; 将基准数挖出形成第一个坑a[i]。
    2. j--由后向前找比它小的数，找到后挖出此数填前一个坑a[i]中。
    3. i++由前向后找比它大的数，找到后也挖出此数填到前一个坑a[j]中。
    4. 再重复执行2，3二步，直到i==j，将基准数填入a[i]中

> 代码实现

```python
def quick_sort(L,start,end):
    if start<end:
        i,j,pivot=start,end,L[start]
        while i<j:
            # 从右开始向左寻找第一个小于pivot的值
            while (i<j) and (L[j]>=pivot):
                j-=1
            # 将小于pivot的值移到左边
            if i<j:
                L[i]=L[j]
                i+=1
            # 从左向右寻找第一个大于pivot的值
            while i<j and L[i]<pivot:
                i+=1
            # 将大于pivot的值移到右边
            if i<j:
                L[i]=L[j]
                j-=1
            # 循环结束后，说明i=j，此时左边值全部小于pivot，右边的值全部大于pivot
            # pivot的位置移动正确，那么此时只需要对左右两侧的序列调用此函数进一步排序即可
            # 递归调用函数 依次对左侧序列 从0到i-1 右侧序列 从i-1到结束
        L[i]=pivot
        # 左侧序列继续排序
        quick_sort(L,start,i-1)
        # 右侧序列继续排序
        quick_sort(L,i+1,end)
```

## 归并排序

![sort_10](/images/python/sort_10.gif)

归并排序是建立在归并操作上的一种有效的排序算法，该算法是采用分治法的一个典型的应用。它的基本操作是：将已有的子序列合并，达到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。

归并排序其实要做两件事：

1. 分解：将序列每次折半拆分
2. 合并：将划分后的序列段两两排序合并

因此，归并排序实际上就是两个操作，拆分+合并

如何合并？

L[first...mid]为第一段，L[mid+1...last]为第二段，并且两端已经有序，现在我们要将两端合成达到L[first...last]并且也有序。

1. 首先依次从第一段与第二段中取出元素比较，将较小的元素赋值给temp[]
2. 重复执行上一步，当某一段赋值结束，则将另一段剩下的元素赋值给temp[]
3. 此时将temp[]中的元素复制给L[]，则得到的L[first...last]有序

如何分解？

在这里，我们采用递归的方法，首先将待排序列分成A,B两组；然后重复对A、B序列分组；直到分组后组内只有一个元素，此时我们认为组内所有元素有序，则分组结束。

## 基数排序

![sort_11](/images/python/sort_11.gif)

基数排序：通过序列中各个元素的值，对排序的N个元素进行若干趟的“分配”与“收集”来实现排序。

1. 分配：我们将L[i]中的元素取出，首先确定其个位上的数字，根据该数字分配到与之序号相同的桶中
2. 收集：当序列中所有的元素都分配到对应的桶中，再按照顺序依次将桶中的元素收集形成新的一个待排序列L[ ]

对新形成的序列L[]重复执行分配和收集元素中的十位、百位...直到分配完该序列中的最高位，则排序结束。
根据上述“基数排序”的展示，我们可以清楚的看到整个实现的过程。
