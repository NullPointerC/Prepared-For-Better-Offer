[剑指 Offer 31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/ "剑指 Offer 31. 栈的压入、弹出序列")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220208004204586-1529412753.png)

连模拟都没有想到了😂。
我们不断将入栈序列$pushed$入栈，若栈顶元素和$popped$遍历到的位置$popped[pos]$相同，则表示找到了同样的出栈序列，则将$pos$加1，否则继续入栈，直到栈顶和遍历至的位置相同。
最后判断$pos$是否遍历完即可。
```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> st = new ArrayDeque<>();
        int pos = 0;
        for(int num : pushed) {
            st.push(num);
            while(pos < popped.length && !st.isEmpty() && st.peek() == popped[pos]) {
                st.pop();
                pos++;
            }
        }
        return pos == popped.length;
    }
}
```