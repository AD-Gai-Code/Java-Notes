# 非明显dp特征题目的dp解法

## [42.接雨水](https://leetcode-cn.com/problems/trapping-rain-water/submissions/)
`l_max[i]` 表示位置`i`左边的最大值， `r_max[i]` 表示位置`i`右边的最大值

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int res = 0;
        int[] l_max = new int[n];
        int[] r_max = new int[n];
        l_max[0] = height[0];
        r_max[n - 1] = height[n - 1];
        for(int i = 1; i < n; i++){
            l_max[i] = Math.max(l_max[i - 1], height[i]);
        }
        for(int i = n - 2; i >= 0; i--){
            r_max[i] = Math.max(r_max[i + 1], height[i]);
        }
        for(int i = 0; i < n; i++){
            res += Math.min(r_max[i], l_max[i]) - height[i];
        }
        return res;
    }
}
// 空间优化，双指针
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int res = 0;
        int l_max = height[0];
        int r_max = height[n - 1];
        int left = 0, right = n - 1;
        while(left <= right){
            l_max = Math.max(l_max, height[left]);
            r_max = Math.max(r_max, height[right]);
            if(l_max < r_max){
                res += l_max - height[left];
                left++;
            } else {
                res += r_max - height[right];
                right--;
            }
        }
        return res;
    }
}
```

