```c++
#include <iostream>
#include <vector>
#include <cmath>
#include <chrono>

// N皇后问题
// 测试链接 : https://leetcode.cn/problems/n-queens-ii/
using namespace std;

// 用数组表示路径实现的N皇后问题，不推荐
int f1(int i, vector<int>& path, int n);

// 检查当前是否有效
bool check(const vector<int>& path, int i, int j);

// N皇后问题，用数组表示路径的版本
int totalNQueens1(int n) {
    if (n < 1) {
        return 0;
    }
    vector<int> path(n, 0);
    return f1(0, path, n);
}

// i: 当前来到的行
// path: 0...i-1行的皇后，都摆在了哪些列
// n: 是几皇后问题
// 返回: 0...i-1行已经摆完了，i....n-1行可以去尝试的情况下还能找到几种有效的方法
int f1(int i, vector<int>& path, int n) {
    if (i == n) {
        return 1;
    }
    int ans = 0;
    for (int j = 0; j < n; j++) {
        if (check(path, i, j)) {
            path[i] = j;
            ans += f1(i + 1, path, n);
        }
    }
    return ans;
}

// 当前在i行、j列的位置，检查是否冲突
bool check(const vector<int>& path, int i, int j) {
    for (int k = 0; k < i; k++) {
        if (j == path[k] || abs(i - k) == abs(j - path[k])) {
            return false;
        }
    }
    return true;
}

// 用位信息表示路径实现的N皇后问题，推荐
int f2(int limit, int col, int left, int right);

// 位运算版本的N皇后求解
int totalNQueens2(int n) {
    if (n < 1) {
        return 0;
    }
    // limit:
    int limit = (1 << n) - 1;
    return f2(limit, 0, 0, 0);
}

// limit: 当前是几皇后问题
// col: 之前皇后的列影响
// left: 之前皇后的右上 -> 左下对角线影响
// right: 之前皇后的左上 -> 右下对角线影响
int f2(int limit, int col, int left, int right) {
    if (col == limit) {
        return 1;
    }
    int ban = col | left | right;
    int candidate = limit & (~ban);
    int place = 0, ans = 0;
    while (candidate != 0) {
        place = candidate & -candidate;
        candidate ^= place;
        ans += f2(limit, col | place, (left | place) >> 1, (right | place) << 1);
    }
    return ans;
}

int main() {
    int n = 14;
    auto start = chrono::high_resolution_clock::now();
    cout << "测试开始" << endl;
    cout << "解决" << n << "皇后问题" << endl;

    // 方法1
    start = chrono::high_resolution_clock::now();
    cout << "方法1答案 : " << totalNQueens1(n) << endl;
    auto end = chrono::high_resolution_clock::now();
    cout << "方法1运行时间 : "
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " 毫秒" << endl;

    // 方法2
    start = chrono::high_resolution_clock::now();
    cout << "方法2答案 : " << totalNQueens2(n) << endl;
    end = chrono::high_resolution_clock::now();
    cout << "方法2运行时间 : "
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " 毫秒" << endl;

    cout << "=======" << endl;
    cout << "只有位运算的版本，才能10秒内跑完16皇后问题的求解过程" << endl;

    start = chrono::high_resolution_clock::now();
    int ans = totalNQueens2(16);
    end = chrono::high_resolution_clock::now();
    cout << "16皇后问题的答案 : " << ans << endl;
    cout << "运行时间 : "
         << chrono::duration_cast<chrono::milliseconds>(end - start).count()
         << " 毫秒" << endl;

    return 0;
}
```
