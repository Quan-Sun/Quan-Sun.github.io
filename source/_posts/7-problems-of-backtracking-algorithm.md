---
title: 7 problems of backtracking algorithm
date: 2019-12-22 10:23:35
tags: 
	- 学习笔记
	- backtracking
categories:
	- Leetcode
---

 A summary for 7 "backtracking" problems in Leetcode.  This kind of problems can be solved in a template. It's easy to find the pattern as following.

~~~c++
// nums: a vector of numbers that you have to traverse
// ans: a vector to record final answer
// path: a vector to record traversing numbers
// start: a start point taht you do DFS in 'nums'
// target: a target of final answer, i.e.the numbers sums to target
void dfs(vector<int>& nums, vector<vector<int>>& ans, int start, int target, vector<int>& path) {
        if (target==0) { // the condition of push path into ans
            ans.push_back(path);
            return;
        }
        
        for (int i = start; i < nums.size(); ++i) {
            if (target > 0) {
                // 'backtarcking - kits': push_back; DFS; pop_back.
                // you will find the next 3 lines of code in all 'backtracking' problems
                path.push_back(nums[i]);
                dfs(nums, ans, i, target-nums[i], path);
                path.pop_back();                
            }
        }
        return;
    }
~~~

#### 17.Letter Combinations of a Phone Number

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

![](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

**Example:**

```
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**Note:**

Although the above answer is in lexicographical order, your answer could be in any order you want.

**Solution:**

~~~c++
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return {};
        vector<vector<char>> d(10);
        d[0] = {' '};
        d[1] = {};
        d[2] = {'a','b','c'};
        d[3] = {'d','e','f'};
        d[4] = {'g','h','i'};
        d[5] = {'j','k','l'};
        d[6] = {'m','n','o'};
        d[7] = {'p','q','r','s'};
        d[8] = {'t','u','v'};
        d[9] = {'w','x','y','z'};
        string cur;
        vector<string> ans;
        dfs(digits, d, 0, cur, ans);
        return ans;
    }
private:
    void dfs(const string& digits,
             const vector<vector<char>>& d,
             int l, string& cur, vector<string>& ans) {
        if (l == digits.length()) {
            ans.push_back(cur);
            return;
        }
        
        for (const char c : d[digits[l] - '0']) {
            cur.push_back(c);
            dfs(digits, d, l+1, cur, ans);
            cur.pop_back();
        }
    }
};
~~~

#### 39.Combination Sum

Given a **set** of candidate numbers (`candidates`) **(without duplicates)** and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

The **same** repeated number may be chosen from `candidates` unlimited number of times.

**Note:**

- All numbers (including `target`) will be positive integers.
- The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

**Example 2:**

```
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        if (candidates.empty()) return ans;
        vector<int> path;
        dfs(candidates, ans, 0, target, path);
        return ans;
    }

private:
    void dfs(vector<int>& candidates, vector<vector<int>>& ans, int start, int target, vector<int>& path) {
        if (target==0) {
            ans.push_back(path);
            return;
        }
        
        for (int i = start; i < candidates.size(); ++i) {
            if (target > 0) {
                path.push_back(candidates[i]);
                dfs(candidates, ans, i, target-candidates[i], path);
                path.pop_back();
                
            }
        }
        return;
    }
};
~~~

#### 40.Combination Sum II

Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:**

- All numbers (including `target`) will be positive integers.
- The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**Example 2:**

```
Input: candidates = [2,5,2,1,2], target = 5,
A solution set is:
[
  [1,2,2],
  [5]
]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        if (candidates.empty()) return ans;
        sort(candidates.begin(), candidates.end());
        vector<int> path;
        dfs(candidates, ans, 0, target, path);
        return ans;
    }

private:
    void dfs(vector<int>& candidates, vector<vector<int>>& ans, int start, int target, vector<int>& path) {
        if (target < 0) return;
        if (target == 0) {
            // auto ret = find(ans.begin(),ans.end(),path);
            // if (ret == ans.end()) 
                ans.push_back(path);
            return;
        }
        
        for (int i = start; i < candidates.size(); ++i) {
            if (i > start && candidates[i] == candidates[i-1]) continue;
            path.push_back(candidates[i]);
            dfs(candidates, ans, i+1, target-candidates[i], path);
            path.pop_back();
        }
        return;
    }
};
~~~

#### 77.Combinations

Given two integers *n* and *k*, return all possible combinations of *k* numbers out of 1 ... *n*.

**Example:**

```
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        if (n < k) return ans;
        vector<int> path(0, k);
        dfs(ans, path, 0, 0, n, k);
        return ans;
    }
    
private:
    void dfs(vector<vector<int>>& ans, vector<int>& path, int start, int num, int n, int k) {
        if (num == k) {
            ans.push_back(path);
            return;
        }
        for (int i = start; i < n; ++i) {
            path.push_back(i + 1);
            dfs(ans, path, i+1, num+1, n, k);
            path.pop_back();
        }
        return;
    }
};
~~~

#### 78.Subsets

Given a set of **distinct** integers, *nums*, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        if (nums.empty()) return ans;
        vector<int> path;
        dfs(ans, path, 0, nums);
        return ans;
    }
    
private:
    void dfs(vector<vector<int>>& ans, vector<int>& path, int start, vector<int>& nums) {
        ans.push_back(path);
        for (int i = start; i < nums.size(); ++i) {
            path.push_back(nums[i]);
            dfs(ans, path, i+1, nums);
            path.pop_back();
        }
        return;
    }
};
~~~

#### 90.Subsets II

Given a collection of integers that might contain duplicates, ***nums\***, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        if (nums.empty()) return ans;
        vector<int> path;
        sort(nums.begin(), nums.end());
        dfs(ans, path, 0, nums);
        return ans;
    }
    
private:
    void dfs(vector<vector<int>>& ans, vector<int>& path, int start, vector<int>& nums) {   
        ans.push_back(path);
        for (int i = start; i < nums.size(); ++i) {
            if (i == start || nums[i] != nums[i - 1]) { 
                path.push_back(nums[i]);
                dfs(ans, path, i+1, nums);
                path.pop_back();
            }
        }
        return;
    }
};
~~~

#### 216.Combination Sum III

Find all possible combinations of ***k*** numbers that add up to a number ***n***, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

**Note:**

- All numbers will be positive integers.
- The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: k = 3, n = 7
Output: [[1,2,4]]
```

**Example 2:**

```
Input: k = 3, n = 9
Output: [[1,2,6], [1,3,5], [2,3,4]]
```

**Solution:**

~~~c++
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> ans;
        if ( k == 0 || n == 0) return ans;
        vector<int> path;
        int sum(0);
        dfs(ans, path, 1, sum, k, n);
        return ans;
    }
    
private:
    void dfs(vector<vector<int>>& ans, vector<int>& path, int start, int sum, int k, int n) {
        if (sum > n) return;
        if (sum == n && path.size() == k){
            ans.push_back(path);
            return;
        }
        for (int i = start; i <= 9; ++i) {
            path.push_back(i);
            sum += i;
            dfs(ans, path, i+1, sum, k, n);
            sum -= i;
            path.pop_back();
        }
        return;
    }
};
~~~

