# DFS

### 1、dfs使用场景

* 遍历
* 排列
* 子集（组合）

### 2、代码框架

```c++
def func() {
	if (达到遍历目标) {
        // 做一些存储结果的操作
        return; // 如果是子集问题，这里存放之后不需要退出
    }
       
    for (遍历所有的条件) { // 遍历条件时，需要关注条件的起始值，对于全排列问题，总是从0开始，但是需要记录元素是否被访问过，如果是组合问题，为了避免出现顺序不同，组成元素相同的情况，条件起始值需要递增
        // 存放到临时队列
        func();
        // pop出来
     }
}
```



### 3、例题

[删除系统中重复文件夹](https://leetcode.cn/problems/delete-duplicate-folders-in-system/submissions/496523488/)

```c++
class Solution {
public:
    struct Trie {
        std::string ser;
        std::unordered_map<string, Trie *> children;
    };

    void build(Trie *root, std::unordered_map<string, int> &mp) {
        if (root == nullptr || root->children.empty()) {
            return;
        }
        std::vector<std::string> v;
        for (const auto &iter:root->children) {
            build(iter.second, mp);
            v.push_back(iter.first + "(" + iter.second->ser+")");
        }
        std::sort(v.begin(), v.end());
        for (const auto &s : v) {
            root->ser += s;
        }
        ++mp[root->ser];
    }

    // 这里是使用dfs，遍历所有节点的ser，如果ser重复，就表示这个节点后面的路径是重复的，就退出递归，否则就记录当前节点的ser
    void deleteTrie(Trie *root, const std::unordered_map<string, int> &mp, std::vector<string> &path, std::vector<std::vector<string>> &res) {
        if (mp.find(root->ser) != mp.end() && mp.find(root->ser)->second >1) {
            return;
        }

        if (!path.empty()) {
            res.push_back(path);
        }
        for (const auto &iter : root->children) {
            path.push_back(iter.first);
            deleteTrie(iter.second, mp, path, res);
            path.pop_back();
        }
    }

    vector<vector<string>> deleteDuplicateFolder(vector<vector<string>>& paths) {
        Trie *root = new Trie();
        for(auto &path : paths) {
            Trie *cur = root;
            for(auto &ele : path) {
                if(!cur->children.count(ele)) {
                    cur->children[ele] = new Trie();
                }
                cur = cur->children[ele];
            }
        }
        std::unordered_map<string, int> res_mp;
        build(root, res_mp);
        std::vector<string> path;
        std::vector<std::vector<string>> res;
        deleteTrie(root, res_mp, path, res);
        return res;
    }
};
```

