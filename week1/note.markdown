### easy
1. 用 add first 或 add last 这套新的 API 改写 Deque 的代码
2. 分析 Queue 和 Priority Queue 的源码
3. 删除排序数组中的重复项（Facebook、字节跳动、微软在半年内面试中考过）
快慢指针，满指针i只用来指向不重复的元素，快指针用来遍历数组，只有在j的位置的元素不等于i时，才需要更新i
class Solution {
    public int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    int i = 0;
    for (int j = 1; j < nums.length; j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1;
    }
}
