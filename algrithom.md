<!-- TOC -->

- [排序算法](#排序算法)
- [算法分析一览表](#算法分析一览表)
- [冒泡排序](#冒泡排序)
- [选择排序](#选择排序)
- [插入排序](#插入排序)
- [希尔排序](#希尔排序)
- [快速排序](#快速排序)
- [归并排序](#归并排序)
- [堆排序](#堆排序)
- [实际应用中的排序](#实际应用中的排序)
    - [1.8之前对基本类型使用改进快排(tuned quicksort)](#18之前对基本类型使用改进快排tuned-quicksort)
    - [1.8之前对对象类型使用归并(modified mergesort )](#18之前对对象类型使用归并modified-mergesort-)
    - [1.8对基本类型数组使用双基准快排(DualPivotQuicksort)](#18对基本类型数组使用双基准快排dualpivotquicksort)
    - [1.8对象类型数组使用ComparableTimSort](#18对象类型数组使用comparabletimsort)

<!-- /TOC -->
## 排序算法

1. 冒泡排序
2. 选择排序
3. 插入排序
4. 希尔排序
5. 快速排序
6. 归并排序
7. 堆排序
8. 基数排序
9. 计数排序

## 算法分析一览表  

排序|时间复杂度|额外空间|是否稳定
-|-|-|-
冒泡|O(n^2) |O(1)|稳定
选择|O(n^2) |O(1)|不稳定
插入|O(n^2) |O(1)|稳定
希尔|O(n^1.5)|O(1)|不稳定
快排|O(nlogn)|O(logn)|不稳定
归并|O(nlogn)|O(n)|稳定
堆排序|O(nlogn)|O(1)|不稳定
基数(lsd/msd)|O(d(n+radix))|-|稳定
计数排序|O(n+k)|-|稳定

## 冒泡排序

1. 常见的情况，每次遍历对相邻逆序进行交换，遍历一遍后尾部最大数值。第二次遍历得到次大值位于倒数第二个位置。经过N次遍历数列有序。总结来说就是两层循环，内层循环用于不断比较交换得到循环内最大(小)值。
(实际的实现也可以从后向前进行遍历，每次得到一个头部的最小值，这样的顺序和其他算法更容易近似对比)
2. 优化之1，如果内层遍历没有发生交换，则已经有序，可以提前结束外层循环，直接得到结果。
3. 优化之2，内层循环遍历的时候记录每次发生交换的最后位置，下次内层循环的时候到这个位置即可终结本次循环，即内部循环提前终结。

## 选择排序
1. 每次从待排序序列中找到一个最小值，交换到待排序序列的首部，相对于冒泡排序这里内部循环只进行一次交换。经过N次(N-1次)外层循环后，整个序列有序。这个交换的过程会导致这个算法是不稳定的。一般这个序列如果使用数组的存储，那么我们进行最值和待排序首部的交换可能导致相同大小元素的位序错乱，(比如 5-5-4进行变换后变成4-5-5，两个5的顺序发生了颠倒)。选择排序的稳定性是一个有争议的地方，数据的存储方式(数组、链表)以及是否进行一定的变种(可能有额外的开销)，都可能实现所谓的稳定的选择排序。

## 插入排序
每次将待排序序列中的一个元素插入到有序序列中，外层循环进行n次插入，内层循环进行插入位置的寻找以及已排序元素的移动。

`一种改进-折半插入排序`，即内层循环寻找插入位置使用折半查找的技术，可以减少比较次数。

## 希尔排序
1. 增量递减插入排序，插入排序可以看作增量为1的希尔排序。
2. `希尔排序的编码实现以及复杂度分析` O(n^1.5)来历?

## 快速排序
快排的思路是利用基准值对序列进行划分，使得基准值归位，左侧都小于等于基准，右侧大于等于基准。可以利用递归的思路对左右两侧序列继续进行快速排序。递归的终点是只有一个元素的序列，自然就是有序的。基准值的选取比较重要，可以选择最左侧、左右侧、或者中值。比如对于一个递增序列，选取最左侧为基准值将导致划分严重不均，算法时间复杂度退化为O(n^2)。为了避免这种情况可以选择中值或者随机一个位置作为基准值(实际实现的时候随机性的计算可能会影响性能，对于基准值的选择也有不同的方式),因此算法的平均时间复杂度为O(nlogn)，但理论上仍有可能发生最差的情况。排序的实际实现中会根据问题规模结合不同的排序算法，比如快排当序列划分到比较小规模的时候可以采用插入排序进行操作。注意快排是不稳定的，其中会进行两侧数据的交换，这一点会导致不稳定。

1. 单路快排

取一个基准值，置换到最左侧，设置一个分界标志位，从前向后遍历，遇到小于基准值的就通过调换调整到前面区域并将标志位后移，分界标志始终将已扫描区域根据基准值分开，左侧小于基准，右侧大于等于基准，等遍历完毕后，将最左侧基准值和分界标志处小于基准值的数据进行交换，基准值正确归位。这里会使得等于基准值的元素都位于一侧，对于有大量重复元素的序列而言划分效果不好，排序效率退化。

2. 双路快排

还是选取一个基准值，但是利用首尾两个指针从两侧向中间遍历。和单路快排的区别主要是对等于基准值元素的处理。`不像1中那样等于基准值的元素一定划分到左侧或者右侧`。

场景一:
`先从右侧找小于基准值的，再从左侧找大于基准值的`，然后进行两元素的交换，重复上述过程至首尾指针相遇。最后将基准值和指针相遇处元素互换(`此时指针相遇处一定小于等于基准值，如果序列中有小于基准值的元素，则此时指针相遇处一定是小于基准值的`)。此场景中等于基准值的的其他元素在遍历过程中没有移动，因此不会将和基准值相等的元素都归到一侧去，`在一定程度上消除了1中的弊端`。

场景二：
上述过程中，对等于基准值的元素没有移动，也可以两侧寻找待移动元素的时候包含基准值，然后进行互换，但是互换之后一定要主动移动指针(否则可能进入死循环，比如两侧都是等于基准值的元素)，重复上述过程至两`指针交错`，最后应将首部的基准值和最后相遇处附近小于等于基准值的元素进行互换(即尾部指针最后指向的元素，此时尾部指针已经和头部指针交错)。此场景中等于基准值的元素会频繁调动，同场景一样`在一定程度上消除了1中的弊端`。

3. 三路快排

选取基准值，从前向后遍历，将区域划分为小于基准值、等于基准值和大于基准值三个区域，记录分界点，不断交换调整位置并更新分界点。一次划分后，所有等于基准值的元素都归位，而不单单是所选取的一个基准值归位，同时`消除了1中的弊端`。

资料：</br>
三路快排的一个资料 https://rerun.me/2013/06/13/quicksorting-3-way-and-dual-pivot/  </br>
DualPivotQuicksort.java中sort方法在某种场景下会利用单基准的传统三路快排。


排序的资料</br>
https://blog.csdn.net/jnu_simba/article/details/9705111</br>
https://blog.csdn.net/rocling/article/details/82832998</br>
https://blog.csdn.net/k_koris/article/details/80585979</br>

4. 双基准快排

双基准快排，顾名思义，一次遍历的过程使用两个不同的基准值pivot1、pivot2(pivot1小于pivot2)，一次划分后结果如下：
```
|  < pivot1  |  pivot1 <= && <= pivot2  |    ?    |  > pivot2  |

```
使得pivot1和pivot2归位，然后对三段区域继续进行递归操作。具体实践中可以在规模足够小的时候进行插入排序而不必一直往下递归。双基准相较于单基准在一次遍历的过程中至少会确定两个元素的位置，如果结合三路快排处理等于基准值情况的思路，可以确定更多的元素位置，但要注意在一次遍历中可能会有更多的比较次数。jdk8中排序算法已经由一种改进快排(划分类似三路快排)更改为一种双基准快排(双基准、三路都使用到)。


1. 简单快排的递归实现(栈模拟递归过程)

(参考jdk-Arrays.sort以及stl排序算法)

## 归并排序
归并排序是把序列从中间分成两部分，左右两侧分别进行归并排序，然后合并左右两侧有序数列，也是一个递归的过程。归并排序是稳定的，接近`基于比较的排序的时间下限O(nlogn)`,最坏情况也是如此，其中merge的过程需要额外的空间辅助O(n)，递归深度O(logn)，因此空间复杂度O(n)+O(logn)=O(n)，时间复杂度为O(nlogn),因为进行logn次merge,每层深度对应的merge序列数目之和也为n。

归并较快排而言时间复杂度比较稳定，不会因序列内容而退化，同时是一个稳定排序算法，但是空间复杂度更高。

(参考jdk-Arrays.sort以及stl排序算法)

## 堆排序
堆有大根堆和小根堆两种，堆是一种近似完全二叉树，大根堆要求父节点值大于等于子节点值，小根堆要求父结点值小于等于子节点值。虽然是树结构，但由于近似完全二叉树的结构，一般使用数组来存放节点，如果从0进行编号，则父节点i对应子节点编号(如果有子节点的话)为2*i+1、2*i+2，子节点i对应父节点编号(i-1)/2向下取整，对于一个size为n的堆，[0...n-1]，最后一个叶子节点为n-1,其父节点为最后一个非叶子节点(n-1-1)/2。堆结构强调有序性，相比较于二叉排序树，堆在查找方面并不具有优势(二叉排序树可以在logn级别完成查找判断),堆在插入数据和删除数据方面时间复杂度都为O(logn)，尾部插入、顶部删除、都要进行后续调整操作，保持堆结构。堆可以用来实现优先级队列，和使用有序数组实现的优先级队列进行比较，后者删除元素O(1)，但是插入元素调整时间较复杂O(n)，因此两者要根据实际场景进行比较抉择。(jdk中堆的操作可以参考PriorityQueue的实现，Java中的优先级队列使用堆结构)

1. 堆插入元素
在尾部插入一个数据，然后从此叶子节点进行不断的上移操作(shift-up)，直到最顶端或者符合堆的大小原则即停止。

2. 堆删除元素
删除头部元素，然后将尾部叶子节点放置头部，堆size--，(或者直接将头尾交换，堆size--)，然后从根节点开始进行不断的下移操作(shift-down)，直到最下端或者符合堆的大小原则即停止。

3. 堆构建
    1. 法1，不断插入元素，进行n次堆插入操作(不断上移)，最后即得到堆
    2. 法2，直接操作原始列表，从最后一个非叶子节点向前开始进行遍历，对每个节点进行下移操作，直到根节点。

## 实际应用中的排序
以Arrays的sort方法为例进行分析：

jdk版本|数组元素数据类型|算法|备注
-|-|-|-
1.7|基本数据类型|改进快排|a tuned quicksort
1.7|对象类型|归并排序| 改进的归并排序
1.8|基本数据类型|DualPivotQuicksort|一种双基准快排
1.8|对象类型|ComparableTimSort | 也可指定使用旧版legacyMergeSort(1.7中的策略)

### 1.8之前对基本类型使用改进快排(tuned quicksort)

```java
/**
    * Sorts the specified array of ints into ascending numerical order.
    * The sorting algorithm is a tuned quicksort, adapted from Jon
    * L. Bentley and M. Douglas McIlroy's "Engineering a Sort Function",
    * Software-Practice and Experience, Vol. 23(11) P. 1249-1265 (November
    * 1993).  This algorithm offers n*log(n) performance on many data sets
    * that cause other quicksorts to degrade to quadratic performance.
    *
    * @param a the array to be sorted
    */
public static void sort(int[] a) {
sort1(a, 0, a.length);
}

```
1. 规模小于7直接使用插入排序
2. 基准值选择,small arrays(40以内)使用中间位置的数值作为基准，big arrays(大于40的情况)使用分段计算中值的方式(the median of medians)
3. 划分算法使得等于基准值的元素均匀分布在两端[v* (<v)* (>v)* v*]，然后进行交换，使得所有等于基准值的元素集中在中间区域[(<v)* v* (>v)*]，也就是每次使得基准以及等于基准的元素都归位。这区别于简单快排很重要的一点。简单快排每个递归层次只是解决单个基准值定位的问题。`划分结果有点类似三路快排`

### 1.8之前对对象类型使用归并(modified mergesort )

```java
/**
    * Sorts the specified array of objects into ascending order, according to
    * the {@linkplain Comparable natural ordering}
    * of its elements.  All elements in the array
    * must implement the {@link Comparable} interface.  Furthermore, all
    * elements in the array must be <i>mutually comparable</i> (that is,
    * <tt>e1.compareTo(e2)</tt> must not throw a <tt>ClassCastException</tt>
    * for any elements <tt>e1</tt> and <tt>e2</tt> in the array).<p>
    *
    * This sort is guaranteed to be <i>stable</i>:  equal elements will
    * not be reordered as a result of the sort.<p>
    *
    * The sorting algorithm is a modified mergesort (in which the merge is
    * omitted if the highest element in the low sublist is less than the
    * lowest element in the high sublist).  This algorithm offers guaranteed
    * n*log(n) performance.
    *
    * @param a the array to be sorted
    * @throws  ClassCastException if the array contains elements that are not
    *		<i>mutually comparable</i> (for example, strings and integers).
    */
public static void sort(Object[] a) {
    Object[] aux = (Object[])a.clone();
    mergeSort(aux, a, 0, a.length, 0);
}


/**
    * Src is the source array that starts at index 0
    * Dest is the (possibly larger) array destination with a possible offset
    * low is the index in dest to start sorting
    * high is the end index in dest to end sorting
    * off is the offset to generate corresponding low, high in src
    */
private static void mergeSort(Object[] src,
                Object[] dest,
                int low,
                int high,
                int off) {....}

注意源码实现中的排序区域是[low,high),而不是前面快排使用的[low,heigh]两个闭区间，因此如果要分析具体代码边界和细节的话需要留意这点，当然我们自己实现不一定非要这样设计。

```
这里首先利用数组克隆得到一个辅助空间，辅助空间用于merge操作(浅拷贝，不过对于我们这里的排序没什么影响，只是拷贝一个引用数组空间，具体指向的对象还是相同的)。

1. 小规模直接使用插入排序(INSERTIONSORT_THRESHOLD = 7)
2. 辅助空间的利用，在递归过程中src和dst两个数组会不断变换角色，分析代码的时候要注意这一点
3. merge的时候进行提前判断，发现两个序列整体有序则直接拷贝回目标数组，而不需逐个merge

### 1.8对基本类型数组使用双基准快排(DualPivotQuicksort)

```java
/**
    * Sorts the specified array into ascending numerical order.
    *
    * <p>Implementation note: The sorting algorithm is a Dual-Pivot Quicksort
    * by Vladimir Yaroslavskiy, Jon Bentley, and Joshua Bloch. This algorithm
    * offers O(n log(n)) performance on many data sets that cause other
    * quicksorts to degrade to quadratic performance, and is typically
    * faster than traditional (one-pivot) Quicksort implementations.
    *
    * @param a the array to be sorted
    */
public static void sort(int[] a) {
    DualPivotQuicksort.sort(a, 0, a.length - 1, null, 0, 0);
}
```
双基准快排比传统单基准快排更为高效。(具体原因得看论文了..)

针对int数据类型分析如下：
1. 小于一定规模直接使用双基准快排(QUICKSORT_THRESHOLD = 286)
2. 大规模首先探测数列有序情况，不那么有序(MAX_RUN_COUNT = 67)则使用双基准快排结束
3. 对于基本有序的情况(count < MAX_RUN_COUNT,count可以理解为连续有序的子序列数目)，直接一次性归并完成。

如果是char以及byte类型数组则对大规模数据使用基数排序
```java

    /**
     * If the length of a byte array to be sorted is greater than this
     * constant, counting sort is used in preference to insertion sort.
     */
    private static final int COUNTING_SORT_THRESHOLD_FOR_BYTE = 29;

    /**
     * If the length of a short or char array to be sorted is greater
     * than this constant, counting sort is used in preference to Quicksort.
     */
    private static final int COUNTING_SORT_THRESHOLD_FOR_SHORT_OR_CHAR = 3200;

```
1. 大于计数排序基准值则直接使用计数排序(空间换时间，适用于数据规模较大且数据范围有限的场景)
2. 否则就是用如前所述的双基准快排(使用插入排序或者探测序列后使用快排或者归并)


### 1.8对象类型数组使用ComparableTimSort

```java
    /**
     * Sorts the specified array of objects into ascending order, according
     * to the {@linkplain Comparable natural ordering} of its elements.
     * All elements in the array must implement the {@link Comparable}
     * interface.  Furthermore, all elements in the array must be
     * <i>mutually comparable</i> (that is, {@code e1.compareTo(e2)} must
     * not throw a {@code ClassCastException} for any elements {@code e1}
     * and {@code e2} in the array).
     *
     * <p>This sort is guaranteed to be <i>stable</i>:  equal elements will
     * not be reordered as a result of the sort.
     *
     * <p>Implementation note: This implementation is a stable, adaptive,
     * iterative mergesort that requires far fewer than n lg(n) comparisons
     * when the input array is partially sorted, while offering the
     * performance of a traditional mergesort when the input array is
     * randomly ordered.  If the input array is nearly sorted, the
     * implementation requires approximately n comparisons.  Temporary
     * storage requirements vary from a small constant for nearly sorted
     * input arrays to n/2 object references for randomly ordered input
     * arrays.
     *
     * <p>The implementation takes equal advantage of ascending and
     * descending order in its input array, and can take advantage of
     * ascending and descending order in different parts of the the same
     * input array.  It is well-suited to merging two or more sorted arrays:
     * simply concatenate the arrays and sort the resulting array.
     *
     * <p>The implementation was adapted from Tim Peters's list sort for Python
     * (<a href="http://svn.python.org/projects/python/trunk/Objects/listsort.txt">
     * TimSort</a>).  It uses techniques from Peter McIlroy's "Optimistic
     * Sorting and Information Theoretic Complexity", in Proceedings of the
     * Fourth Annual ACM-SIAM Symposium on Discrete Algorithms, pp 467-474,
     * January 1993.
     *
     * @param a the array to be sorted
     * @throws ClassCastException if the array contains elements that are not
     *         <i>mutually comparable</i> (for example, strings and integers)
     * @throws IllegalArgumentException (optional) if the natural
     *         ordering of the array elements is found to violate the
     *         {@link Comparable} contract
     */
    public static void sort(Object[] a) {
        if (LegacyMergeSort.userRequested)
            legacyMergeSort(a);
        else
            ComparableTimSort.sort(a, 0, a.length, null, 0, 0);
    }

```
这里保留了旧的归并实现，也就是1.8之前的实现方案，默认会使用新方案也就是ComparableTimSort。这里的排序会在随机数列的情况下和传统归并一样高效，在部分有序的情境下，比较次数远远低于O(nlogn)，辅助空间也会根据序列的有序性有不同变化，综合来说针对数据情形，在时间和空间方面更为高效。

1. 数组规模小于MIN_MERGE(32)则会调整数组然后使用binarySort(折半插入排序)
2. 大规模时直接使用ComparableTimSort，注意这里没有递归的调用，直接循环解决问题。
