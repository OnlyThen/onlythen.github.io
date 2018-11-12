---
layout:     post
title:      "详细分析各种排序算法"
subtitle:   "代码笔记第六话"
date:       2018-04-26
author:     "XiaoYu"
header-img: "img/post-sort.jpeg"
catalog:    true
tags:
    - Mathematics
    - Algorithm
    - Code
---

> 近日突然发现各种排序算法不甚明了，而且各种时间复杂度的分析也不知其解，在此详细作个总结。

## 导论

排序实际上就是对一系列数据集的重新排列的操作，且在计算机内都是按照数值的大小来递增或递减排列，为了对数据执行更快的查询，有必要对数据进行排序。在网上看到一人的总结，觉得分的很好，于是采用他的分类方式:<https://www.cnblogs.com/onepixel/articles/7674659.html>。即分为非线性时间比较类排序和线性时间非比较类排序，下图是比较。
![](/img/in_post/sort.jpeg)

---

## 直接插入排序（Insertion Sort）

---

### 定义

插入排序即在一个已有序数列中插入一个新的值，工作原理则是从后向前依次比较，找到相应的位置。步骤如下

1. 取第一个元素为第一次的已排序数列
2. 取下一个元素为待排数值，从后向前与已排序数列进行比较
3. 如果数列中的数大于待排数值，则将该数向后移一位
4. 重复第三步操作，直到找到数列中某个数小于或等于待排数值
5. 将待排数值插入到该数之后
6. 重复步骤2——5


### C语言实现

```
void insertionSort(int arr[], int length) {

    int preIndex, current;    
    for (var i = 1; i < length; i++) {        
        preIndex = i - 1;        
        current = arr[i];        
        while (preIndex >= 0 && arr[preIndex] > current) {            
            arr[preIndex + 1] = arr[preIndex];            
            preIndex--;        
        }        
        arr[preIndex + 1] = current;    
    }    
    
}
```


### 复杂度分析

由于每次需要取出一个新的值，因此空间复杂度为O(1)。在最好情况下，即序列已经有序的情况下，只需要进行n次比较，因此时间复杂度为O(n)；在最坏情况下，即序列是反向有序的，那么第一次要进行一次比较，第n-1次要进行n-1次比较，总的比较次数为(n^2-n)/2，因此时间复杂度为O(n^2)。由于序列的顺序是随机等概的，因此平均时间复杂度仍为O(n^2)。

### 稳定性分析

根据比较规则，当有两个相同的数在不同的位置时，在排序后先后顺序不变，因此是稳定的。

## 希尔排序（Shell Sort）

---

### 定义

希尔排序是一种插入排序，1959年由Donald Shell[提出](http://penguin.ewu.edu/cscd300/Topic/AdvSorting/p30-shell.pdf)，又被称为缩小增量排序（Diminishing Increment Sort）,主要优化了直接插入排序每次只能移动一位的缺陷。步骤如下：

1. 先选择一个递减的增量序列：D1 D2 ... Dk=1，一般取D1=n/2
2. 依次将序列中距离为D1的数放在同一组，对每一组进行直接插入排序
3. 再将距离为D2的数放在一组，进行直接插入排序
4. 直到取到Dk=1

希尔排序的核心是增量序列的选择，不同的序列会导致不同的时间复杂度。

### C语言实现
采用hibbard增量序列，时间复杂度是O(n^1.5)

```
void shellSort(int arr[], int length) {
    
    int i, j, temp, gap=1;
    while (gap < length/4) {
        gap = gap * 2+1;
    }
    for (gap; gap > 0; gap = (gap-1)/2) {
        for (i = gap; i < length; i += 1) {
            temp = arr[i];
            for (j = i-gap; j > 0 && arr[j] > temp; j -= gap ) {
                arr[j+gap] = arr[j];
            }
            arr[j+gap] = temp;
        }
    }
}
```


### 复杂度分析

同样，使用了辅助空间来保存要比较的数，空间复杂度是O(1);下面主要分析hibbard增量的最坏时间复杂度。
hibbard序列是在1963年提出，文献为《An Empirical Study of Minimal Storage Sorting》，其时间复杂度由Papernov 和Stasevich在1965年证明，论文内容是不认识的语言，其主要内容如下
![](/img/in_post/proof.jpeg)
而在论文On the average-case complexity of Shellsort中给出了通用分析。

### 稳定性分析

由于多次插入排序，我们知道一次插入排序是稳定的，不会改变相同元素的相对顺序，但在不同的插入排序过程中，相同的元素可能在各自的插入排序中移动，最后其稳定性就会被打乱，所以shell排序是不稳定的。

## 选择排序（Selection Sort）

---

### 定义

选择排序很容易想到，即每次选择未排序序列中最大的值放在已排序序列的最右边，以此排序完成。

### C语言实现

```
void selectSort(int A[], int n) {
    register int i,j,min,m;
    for(i=0; i<n-1; i++) {
        min=i;
        for(j=i+1; j<n; j++) {
            if(A[min] > A[j]) {
                min=j;
            }
        }
        if(min != i) {
            swap(&A[min],&A[i]);
                
        }
    }
}
```
### 复杂度分析

第一轮找出最大值需要(n-1)次比较，第二轮需要(n-2)次比较，因此总的比较数目为
![](http://latex.codecogs.com/gif.latex?\1+2+...+(n-1)=\frac12(n^2-n))
同样只需要取一个数据，那么空间复杂度为O(1)。

### 稳定性分析

其实稳定与否取决于实际编写的函数（比较是否含有等于），一般是不稳定的。

## 堆排序（Heapsort）

---

### 定义

指利用堆积树这种数据结构设计的一种排序算法，它是选择排序的一种。堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。
堆排序的基本思想是：将待排序序列构造成一个大顶堆，此时，整个序列的最大值就是堆顶的根节点。将其与末尾元素进行交换，此时末尾就为最大值。然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序序列。

1. 将给定序列写为堆形式
2. 构造为大顶堆
3. 将头元素与尾元素交换，即将最大数提出
4. 重复2、3步，直到完成排序

### C语言实现

```
void adjustHeap(int param1, int j, int inNums[]) {
    int temp = inNums[param1];
    for (int k = param1*2+1; k<j; k=k*2+1) {
        //如果右边值大于左边值，指向右边
        if (k+1<j && inNums[k] < inNums[k+1]) {
            k++;
        }
        //如果子节点大于父节点，将子节点值赋给父节点,并以新的子节点作为父节点（不用进行交换）
        if (inNums[k]>temp) {
            inNums[param1] = inNums[k];
            param1 = k;
        } else {
            break;
        }
    }
     
    inNums[param1] = temp;
}

void HeapSort(int nums, int inNums[]) {
    //1.构建大顶堆
    for (int i = nums/2-1; i >= 0; i--)
    {
                //put the value in the final position
        adjustHeap(i, nums, inNums);
    }
    //2.调整堆结构+交换堆顶元素与末尾元素
    for (int j = nums-1; j>0; j--)
    {
                //堆顶元素和末尾元素进行交换
        int temp = inNums[0];
        inNums[0] = inNums[j];
        inNums[j] = temp;
 
        adjustHeap(0, j, inNums);//重新对堆进行调整
    }
}
```

### 复杂度分析

堆排序是就地排序，辅助空间为O(1)。
堆排序的时间复杂度，主要在初始化建堆过程和每次选取最大数后重新建堆的过程；
初始化建堆过程时间：O(n)。
假设高度为k，则从倒数第二层右边的节点开始，这一层的节点都要执行子节点比较然后交换（如果顺序是对的就不用交换）；倒数第三层呢，则会选择其子节点进行比较和交换，如果没交换就可以不用再执行下去了。如果交换了，那么又要选择一支子树进行比较和交换；
那么总的时间计算为：S = 2^( i - 1 )  *  ( k - i )；其中 i 表示第几层，2^( i - 1) 表示该层上有多少个元素，( k - i) 表示子树上要比较的次数，在最差的条件下，就是比较次数后还要交换；
S = 2^k -k -1 < n,因此这一部分时间复杂度为O(n)。

选取最大数后重新建堆的时间复杂度：O(nlogn)
这一部分的证明集中在O(logn)上，但我发现实际上太大了，这也与《算法导论》中的内容一致。

### 稳定性分析

它是不稳定的排序方法。

## 冒泡排序（Bubble Sort）

---

### 定义

冒泡排序思想很简单，类似于堆排序，从未排序数列的头部开始两两比较，较大的移到后面，那么一轮比较完之后最大的数就出现在序列的最后，接下来进行下一轮，直到排序完毕。

### C语言实现

```
void bubble_sort(int a[], int n) {
    int i, j, temp;
    for (j = 0; j < n - 1; j++) {
        for (i = 0; i < n - 1 - j; i++) {
        
            if(a[i] > a[i + 1]) {
            
                temp = a[i];
                a[i] = a[i + 1];
                a[i + 1] = temp;
            }
        }
     }
}
```

### 复杂度分析

由于需要重复比较，因此时间复杂度是O(n^2)
同样是就地排序，空间复杂度为O(1)。

### 稳定性分析

是稳定的。

## 快速排序（QuickSort）

---

### 定义

快速排序是对冒泡排序的改进，它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序。

1. 设置两个变量i、j，排序开始的时候：i=0，j=N-1；
2. 以第一个数组元素作为关键数据，赋值给key，即key=A[0]；
3. 从j开始向前搜索，即由后开始向前搜索(j--)，找到第一个小于key的值A[j]，将A[j]和A[i]互换；
4. 从i开始向后搜索，即由前开始向后搜索(i++)，找到第一个大于key的A[i]，将A[i]和A[j]互换；
5. 重复第3、4步，直到i=j； (3,4步中，没找到符合条件的值，即3中A[j]不小于key,4中A[i]不大于key的时候改变j、i的值，使得j=j-1，i=i+1，直至找到为止。找到符合条件的值，进行交换的时候i， j指针位置不变。另外，i==j这一过程一定正好是i+或j-完成的时候，此时令循环结束）。

### C语言实现
```
void sort(int *a, int left, int right)
{
    if(left >= right)/*如果左边索引大于或者等于右边的索引就代表已经整理完成一个组了*/
    {
        return ;
    }
    int i = left;
    int j = right;
    int key = a[left];
     
    while(i < j)                               /*控制在当组内寻找一遍*/
    {
        while(i < j && key <= a[j])
        /*而寻找结束的条件就是，1，找到一个小于或者大于key的数（大于或小于取决于你想升
        序还是降序）2，没有符合条件1的，并且i与j的大小没有反转*/ 
        {
            j--;/*向前寻找*/
        }
         
        a[i] = a[j];
        /*找到一个这样的数后就把它赋给前面的被拿走的i的值（如果第一次循环且key是
        a[left]，那么就是给key）*/
         
        while(i < j && key >= a[i])
        /*这是i在当组内向前寻找，同上，不过注意与key的大小关系停止循环和上面相反，
        因为排序思想是把数往两边扔，所以左右两边的数大小与key的关系相反*/
        {
            i++;
        }
         
        a[j] = a[i];
    }
     
    a[i] = key;/*当在当组内找完一遍以后就把中间数key回归*/
    sort(a, left, i - 1);/*最后用同样的方式对分出来的左边的小组进行同上的做法*/
    sort(a, i + 1, right);/*用同样的方式对分出来的右边的小组进行同上的做法*/
                       /*当然最后可能会出现很多分左右，直到每一组的i = j 为止*/
}
```
### 复杂度分析

其平均时间复杂度在的分析主要依赖于离散数学中的*Master Theorem*

### 稳定性分析

它是不稳定的。

## 归并排序（Merge Sort）

---

### 定义

通过递归的方法，将序列分为小序列，在通过合并来将两个小序列合成一个大有序数列。在排序开始时取单个元素为已排序序列，两两合并的方法称为二路归并。步骤如下：

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置；
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置，重复步骤3直到某一指针超出序列尾，将另一序列剩下的所有元素直接复制到合并序列尾

### C语言实现

```
void Merge(int sourceArr[],int tempArr[], int startIndex, int midIndex, int endIndex) {
    int i = startIndex, j=midIndex+1, k = startIndex;
    while(i!=midIndex+1 && j!=endIndex+1) {
        if(sourceArr[i] > sourceArr[j])
            tempArr[k++] = sourceArr[j++];
        else
            tempArr[k++] = sourceArr[i++];
    }
    while(i != midIndex+1)
        tempArr[k++] = sourceArr[i++];
    while(j != endIndex+1)
        tempArr[k++] = sourceArr[j++];
    for(i=startIndex; i<=endIndex; i++)
        sourceArr[i] = tempArr[i];
}
 
//内部使用递归
void MergeSort(int sourceArr[], int tempArr[], int startIndex, int endIndex) {
    int midIndex;
    if(startIndex < endIndex) {
    
        midIndex = (startIndex + endIndex) / 2;
        MergeSort(sourceArr, tempArr, startIndex, midIndex);
        MergeSort(sourceArr, tempArr, midIndex+1, endIndex);
        Merge(sourceArr, tempArr, startIndex, midIndex, endIndex);
    }
}
```

### 复杂度分析

由于需要提出所有的数据进行比较，因此空间复杂度为O(n);
归并排序的思想是分治思想，因此其时间复杂度作如下分析，总时间=分解时间+解决问题时间+合并时间。分解时间就是把一个待排序序列分解成两序列，时间为一常数，时间复杂度O(1).解决问题时间是两个递归式，把一个规模为n的问题分成两个规模分别为n/2的子问题，时间为2T(n/2).合并时间复杂度为O(n)。总时间T(n)=2T(n/2)+O(n).这个递归式可以用递归树来解，其解是O(nlogn)。
这同样使用了*Master Theorem*

### 稳定性分析

在标准情况下，它是稳定的。

## 计数排序（Counting Sort）

---

### 定义

该算法于1954年由 Harold H. Seward 提出。 它是一个不需要比较的，类似于桶排序的线性时间排序算法。该算法是对已知数量范围的数组进行排序。其时间复杂度为O(n)，适用于小范围集合的排序。计数排序是用来排序0到100之间的数字的最好的算法。当然这是一种牺牲空间换取时间的做法。步骤如下

1. 找出待排序的数组中最大和最小的元素；
2. 统计数组中每个值为i的元素出现的次数，存入数组C的第i项；
3. 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素i放在新数组的第C(i)项，每放一个元素就将C(i)减去1。

### C语言实现

```
void CountSort(int data[],int n)
{
    int i, j, count, * data_p;
    data_p = (int*)malloc(sizeof(int)*n);
    for(i=0; i<n; i++)//初始化data_p
        data_p[i]=0;
    for(i=0; i<n; i++)
    {
        count=0;
        for(j=0;j<n;j++)//扫描待排序数组
            if(data[j]<data[i])//统计比data[i]值小的值的个数
                count++;
        if(data_p[count]!=0)//对于相等非0的数据，应向后措一位。数据为0时，因数组data_p被初始化为0，故不受影响。
            count++;
        data_p[count]=data[i];//存放到data_p中的对应位置
    }
        //用于检查当有多个数相同时的情况
    i=0,j=n;
    while(i<j)
        {
        if(data_p[i]==0) {
                
            temp=i-1;
            data_p[i]=data_p[temp];
        }//of if
        i++;
    }//of  while
    for(i=0;i<n;i++)//把排序完的数据复制到data中
        data[i]=data_p[i];
    free(data_p);//释放data_p
}
```

### 复杂度分析

由于需要外部数组保存数据，因此空间复杂度为O(n+k)，k是序列的最大值。
这种方式需要的时间为O(n+k)。

### 稳定性分析

这种方式是稳定的。

## 桶排序（Bucket Sort）

---

### 定义

桶排序是对计数排序的改进，同样适用于数值范围小，序列数列大的情况。步骤如下：

1. 申请一定数量的桶；
2. 按照一个设定好的映射函数，将序列里面的元素映射到对应的桶里，如按照数的十位数；
3. 遍历一遍所有的桶，将各个桶内的元素进行排序，排序算法可以采用冒泡排序，插入排序，快速排序，归并排序等等适合的算法；
4. 将所有桶内的元素按照顺序组合起来就是我们需要的有序序列。

### C语言实现
```
	1	void BucketSort(int* arr , int len)  
	2	{  
	3	    int tmpArrLen = GetMaxVal(arr , len) + 1;  //取数据最大值
	4	    int tmpArr[tmpArrLen];  //获得空桶大小  
	5	    int i, j;  
	6	      
	7	    for( i = 0; i < tmpArrLen; i++)  //空桶初始化  
	8	        tmpArr[i] = 0;  
	9	      
	10	    for(i = 0; i < len; i++)   //寻访序列，并且把项目一个一个放到对应的桶子去。  
	11	        tmpArr[ arr[i] ]++;  
	12	      
	13	    for(i = 0, j = 0; i < tmpArrLen; i ++)  
	14	    {  
	15	        while( tmpArr[ i ] != 0) //对每个不是空的桶子进行排序。  
	16	        {  
	17	            arr[j ] = i;  //从不是空的桶子里把项目再放回原来的序列中。  
	18	            j++;  
	19	            tmpArr[i]--;  
	20	        }  
	21	    }  
	22	}  
```

### 复杂度分析

这是一种空间交换时间的算法，空间复杂度为O(n+k)。
桶排序最好情况下使用线性时间O(n)，桶排序的时间复杂度，取决与对各个桶之间数据进行排序的时间复杂度，因为其它部分的时间复杂度都为O(n)。很显然，桶划分的越小，各个桶之间的数据越少，排序所用的时间也会越少。但相应的空间消耗就会增大。 

### 稳定性分析

它是稳定的。

## 基数排序（Radix Sort）

---

### 定义

它是透过键值的部分信息，将要排序的元素分配至某些“桶”中，藉以达到排序的作用，它有两种方法：最高位优先(Most Significant Digit first)法，简称MSD法：先按k1排序分组，同一组中记录，关键码k1相等，再对各组按k2排序分成子组，之后，对后面的关键码继续这样的排序分组，直到按最次位关键码kd对各子组排序后。再将各组连接起来，便得到一个有序序列。
最低位优先(Least Significant Digit first)法，简称LSD法：先从kd开始排序，再对kd-1进行排序，依次重复，直到对k1排序后便得到一个有序序列。

### C语言实现

```
voidbucketSort3(int *p, intn)
{
    //获取数组中的最大数
    intmaxNum = findMaxNum(p, n);
    //获取最大数的位数，次数也是再分配的次数。
    intloopTimes = getLoopTimes(maxNum);
    inti;
    //对每一位进行桶分配
    for(i = 1; i <= loopTimes; i++)
    {
        sort2(p, n, i);
    }
}
//获取数字的位数
intgetLoopTimes(intnum)
{
    intcount = 1;
    inttemp = num / 10;
    while(temp != 0)
    {
        count++;
        temp = temp / 10;
    }
    returncount;
}
//查询数组中的最大数
intfindMaxNum(int *p, intn)
{
    inti;
    intmax = 0;
    for(i = 0; i < n; i++)
    {
        if(*(p + i) > max)
        {
            max = *(p + i);
        }
    }
    returnmax;
}
//将数字分配到各自的桶中，然后按照桶的顺序输出排序结果
voidsort2(int *p, intn, intloop)
{
    //建立一组桶此处的20是预设的根据实际数情况修改
    intbuckets[10][20] = {};
    //求桶的index的除数
    //如798个位桶index=(798/1)%10=8
    //十位桶index=(798/10)%10=9
    //百位桶index=(798/100)%10=7
    //tempNum为上式中的1、10、100
    inttempNum = (int)pow(10, loop - 1);
    inti, j;
    for(i = 0; i < n; i++)
    {
        introw_index = (*(p + i) / tempNum) % 10;
        for(j = 0; j < 20; j++)
        {
            if(buckets[row_index][j] == NULL)
            {
                buckets[row_index][j] = *(p + i);
                break;
            }
        }
    }
    //将桶中的数，倒回到原有数组中
    intk = 0;
    for(i = 0; i < 10; i++)
    {
        for(j = 0; j < 20; j++)
        {
            if(buckets[i][j] != NULL)
            {
                *(p + k) = buckets[i][j];
                buckets[i][j] = NULL;
                k++;
            }
        }
    }
}
```

### 复杂度分析

它在最坏情况下的时间复杂度为O(wn),w为数值的平均长度。
空间复杂度为O(n+k)。

### 稳定性分析

同样是稳定的。

