### hashmap的数据插入原理是怎么样的？

![image-20220309202946733](https://gitee.com/cao_ziqiang/img/raw/master/20220309202946.png)

如果链表长度大于8，转化为红黑树；

如果红黑树中节点个数小于6，转回链表；

以上条件都要基于数组的长度大于64时，才会进行转化；

### HashMap怎么设定初始容量大小?

一般如果new HashMap()不传值，默认大小是16，负载因子是0.75。

如果自己传入初始大小k，初始化大小为大于k的2的整数次方，例如如果传10，大小为16。

![image-20220309210013740](https://gitee.com/cao_ziqiang/img/raw/master/20220309210013.png)

