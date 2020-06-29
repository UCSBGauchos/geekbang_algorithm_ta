### 最近公共祖先：
子问题拆解
左右字数都分别p,q，如果pq都包含在左子树中，则该根节点已经不是最近公共祖先了
### 不要人肉递归
class Solution {
public:
    TreeNode* ans;
    bool dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 终止条件
        if (root == nullptr) return false;
       //子问题drill down
        bool lson = dfs(root->left, p, q);
        bool rson = dfs(root->right, p, q);
        // 当前层的process处理逻辑，只有当左子树和右子树分别包含p, q。或者是root节点自己是p或者p，那只需要左子树和右子树有一个就行了。就将当前root节点标记为目标节点
        if ((lson && rson) || ((root->val == p->val || root->val == q->val) && (lson || rson))) {
            ans = root;
        } 

       // reverse
       不需要清扫，清扫层为空
        
        // 这边判断的条件是root=p或者q，或者是将子树的节点传导上去
        return lson || rson || (root->val == p->val || root->val == q->val);
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        dfs(root, p, q);
        return ans;
    }
};

### 从前序与中序遍历序列构造二叉树
1. 使用前序节点确定根节点
2. 使用中序节点确定左子树和右子树的范围
class Solution {
    private Map<Integer, Integer> indexMap;

    public TreeNode myBuildTree(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        // 还是老套路，递归的终止条件先写上
        if (preorder_left > preorder_right) {
            return null;
        }

        // 当前层的处理逻辑，首先先从前序队列中获取根节点
        // 以及在中序队列中确认左右子树的范围
        int preorder_root = preorder_left;
        int inorder_root = indexMap.get(preorder[preorder_root]);
        TreeNode root = new TreeNode(preorder[preorder_root]);
        int size_left_subtree = inorder_root - inorder_left;
        // 确认范围后，drill down，递归求左右子树
        root.left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        root.right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        // reverse，这里不需要清理现场，有点尾递归的感觉
        // 归去来兮，返回上一层
        return root;
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        // 构造哈希映射，帮助我们快速定位根节点
        indexMap = new HashMap<Integer, Integer>();
        for (int i = 0; i < n; i++) {
            indexMap.put(inorder[i], i);
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }
}

### 组合
从1-n个数中，选出k个数，输出所有可能性
class Solution {
  List<List<Integer>> output = new LinkedList();
  int n;
  int k;

  public void backtrack(int first, LinkedList<Integer> curr) {
    // 还是老套路，递归的终止条件，发现curr里面的尝试长度=k了，就是一个合法组合，放入到output里面去，output是结果集合
    if (curr.size() == k)
      output.add(new LinkedList(curr));

    // 当前层的尝试，由于这里的可能性比较多，所以尝试需要for循环，其实也并没有什么特殊的
    for (int i = first; i < n + 1; ++i) {
      curr.add(i);
      // drill down到下一层尝试
      backtrack(i + 1, curr);
      // reverse，清理现场，这个尝试过了，试试下一个
      // 这个rerverse是回溯法的精髓
      curr.removeLast();
    }
  }

  public List<List<Integer>> combine(int n, int k) {
    this.n = n;
    this.k = k;
    backtrack(1, new LinkedList<Integer>());
    return output;
  }
}
// 这道题目是一个典型的回溯法，所谓回溯法，一种通过探索所有可能的候选解来找出所有的解的算法。如果候选解被确认不是一个解的话（或者至少不是最后一个解），回溯算法会通过在上一步进行一些变化抛弃该解，即回溯并且再次尝试。

### 全排列
同样也是一道比较经典的回溯法的题目，直接来看代码
class Solution {
  public void backtrack(int n,
                        ArrayList<Integer> output,
                        List<List<Integer>> res,
                        int first) {
    // 还是这个套路，终止条件是当尝试队列长度=n的时候，就将这个结果放到res中
    if (first == n)
      res.add(new ArrayList<Integer>(output));
    for (int i = first; i < n; i++) {
      // 如果选取了，就交换
      Collections.swap(output, first, i);
      // 继续递归填下一个数
      backtrack(n, output, res, first + 1);
      // 不选取，撤销操作
      Collections.swap(output, first, i);
    }
  }
  
  public List<List<Integer>> permute(int[] nums) {
    //存放所有可能
    List<List<Integer>> res = new LinkedList();
    ArrayList<Integer> output = new ArrayList<Integer>();
    for (int num : nums)
      output.add(num);
    int n = nums.length;
    backtrack(n, output, res, 0);
    return res;
  }
}

### 全排列 2
这道题目拿出来分析的原因是因为这道题目有很经典的剪枝部分
这类题目的重点就在一，一定要明白哪里可以剪枝
public class Solution {

    public List<List<Integer>> permuteUnique(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        // 排序（升序或者降序都可以），排序是剪枝的前提
        Arrays.sort(nums);

        boolean[] used = new boolean[len];
        // 使用 Deque 是 Java 官方 Stack 类的建议
        Deque<Integer> path = new ArrayDeque<>(len);
        dfs(nums, len, 0, used, path, res);
        return res;
    }

    private void dfs(int[] nums, int len, int depth, boolean[] used, Deque<Integer> path, List<List<Integer>> res) {
        if (depth == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = 0; i < len; ++i) {
            if (used[i]) {
                continue;
            }

            // 剪枝条件：i > 0 是为了保证 nums[i - 1] 有意义
            // 写 !used[i - 1] 是因为 nums[i - 1] 在深度优先遍历的过程中刚刚被撤销选择
            // 举个例子，1和1撇，之前11撇的组合已经选过，现在1撇先进来，1又进来了，同层的，直接剪掉
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                continue;
            }

            path.addLast(nums[i]);
            used[i] = true;

            dfs(nums, len, depth + 1, used, path, res);
            // 回溯部分的代码，和 dfs 之前的代码是对称的
            used[i] = false;
            path.removeLast();
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] nums = {1, 1, 2};
        List<List<Integer>> res = solution.permuteUnique(nums);
        System.out.println(res);
    }
}
