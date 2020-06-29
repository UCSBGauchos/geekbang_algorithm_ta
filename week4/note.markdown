基因库
从目标库中，选一个bank[i]，和start diff为1的，尝试一下，如果发现这种尝试失败了（start 一步变不到bank[i]），就回溯到上一层（记得清理现场），尝试bank中下一个bank[i]，再去和start比。否则成功的话，就把这个bank[i]（start一步变成的），dfs到下一层作为下一层的start
一旦发现start和end一样了，在终止条件中记录目前为止count的最小值。
这是我的理解，不知道这样讲是不是清楚