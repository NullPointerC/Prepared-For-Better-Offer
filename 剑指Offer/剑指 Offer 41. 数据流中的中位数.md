[剑指 Offer 41. 数据流中的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220131233209347-2050336184.png)

不愧是困难题哇😂,确实有点难。一开始想的就是比较暴力的每次求mid的时候都来sort，但是众所周知sort时间复杂度为$O(nlogn)$，又会调用多次的mid，所以这里不使用这个办法。
还有就是可以在插入的时候想办法，定义一个超级大的arr,在每次插入时找到num应该插入的位置，将其插入，平均时间复杂度为$O(n)$, 已经改良了许多。
思路是在插入的时候二分找到插入的位置或者就是插入排序的方式来找到插入的位置，代码如下：
```java
class MedianFinder {
    private int size;
    private int[] arr;
    /** initialize your data structure here. */
    public MedianFinder() {
        this.size = 0;
        this.arr = new int[50010];
    }
    
    public void addNum(int num) {
        // 插入排序找到num应该插入的位置
        int idx = this.size - 1;
        // 此时有size个元素,索引位置应该从size - 1处开始
        while(idx >= 0) {
            if(this.arr[idx] >= num) {
                idx--;
                continue;
            } else if(this.arr[idx] < num) {
                break;
            }
        }
        // 前面的是为了找到第一个小于num的索引,要插入num就应该从第一个大于的地方开始,所以idx++
        idx++;
        // 往后移动
        for(int i = size; i > idx; i--) {
            this.arr[i] = this.arr[i - 1];
        }
        // 设置值
        this.arr[idx] = num;
        this.size++;
    }
    
    public double findMedian() {
        if(this.size % 2 == 1) {
            return this.arr[this.size / 2] * 1.0;
        } else {
            return (this.arr[this.size / 2 - 1] + this.arr[this.size / 2]) / 2.0;
        }
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```
二分的做法类似，不过是把查找的过程从$O(n)$优化成了$O(logn)$，但是实际上时间复杂度取决于重排的过程，所以影响并不是很大。

接下来就是双堆的做法，这个做法太太太秒了😂,本题创建两个堆，一个是小根堆，一个是大根堆，始终让两个堆的大小不超过1,这里为了方便，我们让大根堆比小根堆大1。
接下来我们分析，我们使数组中更小的那一半存储在大根堆中，更大的那一步存储在小根堆中，这样就可以方便的取到中间值。😂
接下来又要考虑的就是入堆顺序的问题，因为我这里假设的是大根堆比小根堆数量多至多1个，所以当二者大小一样时，是需要往大根堆中填充数字，但是这里要注意的是，我们需要保证小根堆中的所有数都大于大根堆，所以将num先放入小根堆，接下来将小根堆中最小的出堆放入大根堆，这样就保证了是准确的中间值，对于小根堆的插入过程同理。
```java
class MedianFinder {

    // 大顶堆
    private PriorityQueue<Integer> pq1;
    // 小顶堆
    private PriorityQueue<Integer> pq2;


    /** initialize your data structure here. */
    public MedianFinder() {
        this.pq1 = new PriorityQueue<>((a, b) -> b - a);
        this.pq2 = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        // 使得大根堆的数量始终比小根堆的数量大于1或者等于1
        if((pq1.size() + pq2.size()) % 2 == 0) {
            // 两个堆大小相等,需要使大根堆数量增加,但是为了维护平衡,需要先放入小根堆中,再从中取出最小的放入大根堆
            pq2.offer(num);
            pq1.offer(pq2.poll());
        } else {
            // 两个堆一个大一个小,需要使小根堆数量增加,同理,需要先放入大根堆,将最大的放回小根堆
            pq1.offer(num);
            pq2.offer(pq1.poll());
        }
    }
    
    public double findMedian() {
        if(pq1.size() - pq2.size() == 1) {
            return pq1.peek() * 1.0;
        } else {
            return (pq1.peek() + pq2.peek()) / 2.0;
        }
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```
