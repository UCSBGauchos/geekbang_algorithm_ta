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