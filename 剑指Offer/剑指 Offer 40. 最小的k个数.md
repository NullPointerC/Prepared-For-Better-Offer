[剑指 Offer 40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220131151315931-1980976236.png)

做这题有很多办法,如果内置了sort函数的语言,就比较简单,可以先排序,再取前k个数即可。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] ans = new int[k];
        Arrays.sort(arr);
        for(int i = 0; i < k; i++) ans[i] = arr[i];
        return ans;
    }
}
```
时间复杂度为$O(nlogn)$，空间复杂度为$O(k)$。

或者手写快排，这里正好复习一下快速排序的原理。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] ans = new int[k];
        quickSort(arr, 0, arr.length - 1);
        for(int i = 0; i < k; i++) ans[i] = arr[i];
        return ans;
    }

    private void quickSort(int[] arr, int lo, int hi) {
        if(lo >= hi) return ;
        int l = lo - 1, r = hi + 1, pivot = arr[lo + hi >> 1];
        while(l < r) {
            do l++; while(arr[l] < pivot);
            do r--; while(arr[r] > pivot);
            if(l < r) {
                int tmp = arr[l];
                arr[l] = arr[r];
                arr[r] = tmp;
            }
        }
        quickSort(arr, lo, r);
        quickSort(arr, r + 1, hi);
    }
}
```
快排属于分治问题，分治一般分为3步：
①.划分为子问题;
②.递归处理子问题;
③.合并子问题;
```cpp
void quick_sort(int q[], int l, int r)
{
    //递归的终止情况
    if(l >= r) return;
    //第一步：分成子问题
    int i = l - 1, j = r + 1, x = q[l + r >> 1];
    while(i < j)
    {
        do i++; while(q[i] < x);
        do j--; while(q[j] > x);
        if(i < j) swap(q[i], q[j]);
    }
    //第二步：递归处理子问题
    quick_sort(q, l, j), quick_sort(q, j + 1, r);
    //第三步：子问题合并.快排这一步不需要操作，但归并排序的核心在这一步骤
}
```
证明过程如下[link](https://www.acwing.com/solution/content/16777/)
也可以使用大根堆，如果堆的大小小于k，则先将数字入堆，否则，比较当前元素和堆顶元素大小，堆顶是目前这7个元素中最大的元素，如果当前元素比堆顶的大，那么肯定不是前k个元素，直接跳过即可，如果比堆顶元素要小，先将堆顶元素出堆，这样才能使更小的元素入堆，最后重复上述过程，直到遍历完数组，再将堆中元素返回即可。
```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 默认是小根堆，实现大根堆需要重写一下比较器。
        Queue<Integer> pq = new PriorityQueue<>((v1, v2) -> v2 - v1);
        for (int num: arr) {
            if (pq.size() < k) {
                pq.offer(num);
            } else if (num < pq.peek()) {
                pq.poll();
                pq.offer(num);
            }
        }
        
        // 返回堆中的元素
        int[] res = new int[pq.size()];
        int idx = 0;
        for(int num: pq) {
            res[idx++] = num;
        }
        return res;
    }
}
```
时间复杂度$O(nlogk)$,空间复杂度$O(k)$。
