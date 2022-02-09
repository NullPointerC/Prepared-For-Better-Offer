[剑指 Offer 59 - II. 队列的最大值](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/comments/ "剑指 Offer 59 - II. 队列的最大值")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220210005931887-2090880172.png)
用一个queue和一个deque来实现，queue用来正常的push_back和pop_front，deque用来存储最大值。
如果新加入的value比deque的尾端更大，那么deque就一直在尾端出队，直到尾端的值比value更大或者队列为空。
再将value压入deque的尾部，每次取max_value都取deque首部的值。
当出队时，如果queue首部的值等于deque的首部值，需要将相同值出队，因为该值已经不存在了。
```java
class MaxQueue {
    private Queue<Integer> valQueue;
    private Deque<Integer> maxDeque;
    public MaxQueue() {
        valQueue = new ArrayDeque<>();
        maxDeque = new ArrayDeque<>();
    }
    
    public int max_value() {
        if(maxDeque.isEmpty()) {
            return -1;
        } else {
            return maxDeque.peek();
        }
    }
    
    public void push_back(int value) {
        valQueue.offer(value);
        int curMax = max_value();
        while(!maxDeque.isEmpty() && value > maxDeque.peekLast()) {
            maxDeque.pollLast();
        }
        maxDeque.offer(value);
    }
    
    public int pop_front() {
        if(valQueue.isEmpty()) {
            return -1;
        }
        int val = valQueue.poll();
        if(maxDeque.peek() == val) {
            maxDeque.poll();
        }
        return val;
    }
}

/**
 * Your MaxQueue object will be instantiated and called as such:
 * MaxQueue obj = new MaxQueue();
 * int param_1 = obj.max_value();
 * obj.push_back(value);
 * int param_3 = obj.pop_front();
 */
```