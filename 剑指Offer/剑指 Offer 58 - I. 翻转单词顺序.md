[剑指 Offer 58 - I. 翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)
![](https://img2022.cnblogs.com/blog/2272548/202201/2272548-20220129221930989-2120239537.png)

人生苦短，我用python

```python3
class Solution:
    def reverseWords(self, s: str) -> str:
        return " ".join(s.split()[::-1])
```
但是这样的方式太取巧了，对语言要求也高，所以就不用这种方法了。
用Java的话就很多细节需要考虑，首先就需要用`trim`去除空格，然后再遍历的时候，如果碰到了连续的空格，也需要只计算一个空格。

```java
class Solution {
    public String reverseWords(String s) {
        if(null == s || s.isBlank()) {
            return s.trim();
        }
        List<String> words = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        for(var c : s.trim().toCharArray()) {
            if(c == ' ') {
                if(sb.length() > 0) {
                    words.add(sb.toString());
                    sb.setLength(0);
                } else {
                    continue;
                }
            } else {
                sb.append(c);
            }
        }
        words.add(sb.toString());
        String[] strs = new String[words.size()];
        for(int i = 0; i < words.size(); i++) {
            strs[i] = words.get(words.size() - 1 - i);
        }
        return String.join(" ", strs).trim();
    }
}
```