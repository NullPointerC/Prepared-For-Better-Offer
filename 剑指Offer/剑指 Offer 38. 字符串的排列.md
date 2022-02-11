[剑指 Offer 38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/ "剑指 Offer 38. 字符串的排列")
![](https://img2022.cnblogs.com/blog/2272548/202202/2272548-20220211200643008-308022119.png)
和含有重复数组的组合问题是同样的思路，需要在同一树层上去重，因为会重复。
```java
class Solution {
    public String[] permutation(String s) {
        if(s == null || s.equals("")) {
            return new String[0];
        }
        char[] str = s.toCharArray();
        Arrays.sort(str);
        List<String> res = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        backtrack(res, sb, new boolean[str.length], str);
        String[] ress = new String[res.size()];
        for(int i = 0; i < res.size(); i++) {
            ress[i] = res.get(i);
        }
        return ress;
    }

    private void backtrack(List<String> res, StringBuilder path, boolean[] used, char[] str) {
        if(path.length() == str.length) {
            res.add(path.toString());
            return ;
        }
        for(int i = 0; i < str.length; i++) {
            if(used[i] || (i > 0 && str[i] == str[i - 1] && !used[i - 1])) {
                continue;
            }
            path.append(str[i]);
            used[i] = true;
            backtrack(res, path, used, str);
            used[i] = false;
            path.deleteCharAt(path.length() - 1);
        }
    }
}
```