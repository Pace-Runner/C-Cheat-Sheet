# C++ LeetCode Syntax Cheatsheet (Easy–Medium)

## 1. Boilerplate & I/O

```cpp
#include <bits/stdc++.h> // pulls in everything (vector, map, algorithm, etc.) — fine for LeetCode
using namespace std;

int main() {
    int x;
    cin >> x;                    // read
    cout << x << endl;           // print
    cout << x << "\n";           // faster than endl (no flush)
    return 0;
}
```
On LeetCode you usually just write a function inside a `class Solution { public: ... };` — no `main`, no I/O needed. I/O above is for when you're testing locally.

---

## 2. Basic Types

```cpp
int, long, long long, size_t   // size_t is unsigned, returned by .size()
double, float
bool
char
string                         // NOT char[] — use std::string
```

Common gotchas:
- `INT_MAX`, `INT_MIN`, `LLONG_MAX` from `<climits>`
- Integer overflow: use `long long` for sums that could exceed ~2 billion
- `(int)v.size() - 1` — careful comparing `size_t` (unsigned) to negative ints, causes bugs

---

## 3. Vectors (dynamic arrays — your default container)

```cpp
vector<int> v;                        // empty
vector<int> v(5);                     // size 5, all zero
vector<int> v(5, -1);                 // size 5, all -1
vector<int> v = {1, 2, 3};

v.push_back(4);
v.pop_back();
v.size();
v.empty();
v[2];                                  // no bounds check
v.at(2);                               // bounds-checked (throws)
v.back(); v.front();

sort(v.begin(), v.end());
sort(v.rbegin(), v.rend());           // descending
reverse(v.begin(), v.end());

// 2D vector
vector<vector<int>> grid(rows, vector<int>(cols, 0));

// iterate
for (int x : v) { ... }
for (int &x : v) { x *= 2; }          // modify in place
for (int i = 0; i < v.size(); i++) { ... }
```

---

## 4. Strings

```cpp
string s = "hello";
s.size(); s.length();
s[0];                          // char
s.substr(start, len);         // substring
s += "abc";                    // concat
s.push_back('x');
to_string(123);                // int -> string
stoi(s);                       // string -> int
stol(s); stoll(s);             // -> long / long long

// compare
s1 == s2;                      // works directly, unlike C

// build strings efficiently
string s2(10, 'a');            // "aaaaaaaaaa"

// reverse
reverse(s.begin(), s.end());

// char checks (<cctype>)
isalpha(c); isdigit(c); isupper(c); islower(c);
tolower(c); toupper(c);

// split isn't built-in — do it manually with stringstream:
#include <sstream>
stringstream ss(s);
string word;
while (ss >> word) { ... }     // splits on whitespace
```

---

## 5. Pairs & Tuples

```cpp
pair<int, int> p = {1, 2};
p.first; p.second;

vector<pair<int,int>> v;
v.push_back({1,2});

tuple<int, int, string> t = {1, 2, "hi"};
get<0>(t); get<1>(t); get<2>(t);
```

---

## 6. Maps (hash map / ordered map)

```cpp
// unordered_map = hash map, O(1) avg — use this by default
unordered_map<int, int> m;
m[key] = value;                    // insert/update
m[key]++;                          // common for counting
if (m.find(key) != m.end()) { }    // check existence
if (m.count(key)) { }              // shorter, same thing
m.erase(key);

for (auto &[k, v] : m) { ... }     // C++17 structured bindings
for (auto &p : m) { p.first; p.second; }

// map = ordered (red-black tree), O(log n), sorted by key
map<int, int> m;                   // iterating gives sorted key order
```

Common pattern — frequency count:
```cpp
unordered_map<char, int> freq;
for (char c : s) freq[c]++;
```

---

## 7. Sets

```cpp
unordered_set<int> s;              // hash set, O(1) avg, no order
s.insert(5);
s.count(5);                        // 0 or 1
s.erase(5);

set<int> s;                        // sorted, O(log n)
s.begin(); s.rbegin();             // smallest / largest element
```

---

## 8. Stack / Queue / Deque

```cpp
stack<int> st;
st.push(1); st.pop(); st.top(); st.empty();

queue<int> q;
q.push(1); q.pop(); q.front(); q.back(); q.empty();

deque<int> dq;                     // double-ended, O(1) both ends
dq.push_front(1); dq.push_back(2);
dq.pop_front(); dq.pop_back();
```

---

## 9. Priority Queue (Heap)

```cpp
priority_queue<int> pq;                       // max-heap by default
pq.push(5); pq.top(); pq.pop();

priority_queue<int, vector<int>, greater<int>> minHeap;   // min-heap

// custom comparator (e.g. for pairs, sort by second value ascending)
auto cmp = [](pair<int,int> a, pair<int,int> b) {
    return a.second > b.second;   // "greater" here means smaller priority in a max-heap-based syntax -> gives min-heap on .second
};
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

---

## 10. Sorting

```cpp
sort(v.begin(), v.end());                          // ascending
sort(v.begin(), v.end(), greater<int>());          // descending

sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;                                  // custom comparator: true if a should come BEFORE b
});

// sort a vector of pairs by second element
sort(v.begin(), v.end(), [](pair<int,int>&a, pair<int,int>&b){
    return a.second < b.second;
});
```
Comparator returning `a < b` semantics = ascending. Must be a strict weak ordering (no `<=`).

---

## 11. Linked Lists

LeetCode gives you this struct already defined:
```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};
```

Traversal:
```cpp
ListNode* cur = head;
while (cur != nullptr) {
    // use cur->val
    cur = cur->next;
}
```

Common patterns:
```cpp
// dummy head (avoids edge cases when modifying list head)
ListNode dummy(0);
dummy.next = head;
ListNode* prev = &dummy;

// reverse a linked list
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* cur = head;
    while (cur) {
        ListNode* nxt = cur->next;
        cur->next = prev;
        prev = cur;
        cur = nxt;
    }
    return prev;
}

// fast/slow pointer (cycle detection / middle of list)
ListNode* slow = head, *fast = head;
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
```

---

## 12. Binary Trees

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```

Recursive DFS (most tree problems):
```cpp
void dfs(TreeNode* node) {
    if (!node) return;
    dfs(node->left);
    // process node->val
    dfs(node->right);
}
```

BFS (level order) with queue:
```cpp
queue<TreeNode*> q;
q.push(root);
while (!q.empty()) {
    int sz = q.size();
    for (int i = 0; i < sz; i++) {
        TreeNode* node = q.front(); q.pop();
        // process node
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## 13. Common Algorithms Library (`<algorithm>`)

```cpp
max(a, b); min(a, b);
max_element(v.begin(), v.end());       // returns iterator, deref with *
min_element(v.begin(), v.end());
accumulate(v.begin(), v.end(), 0);      // sum, needs <numeric>, start value 0
count(v.begin(), v.end(), x);           // count occurrences of x
find(v.begin(), v.end(), x);            // returns iterator, compare to v.end()
unique(v.begin(), v.end());             // removes consecutive dupes (sort first)
lower_bound(v.begin(), v.end(), x);     // first element >= x (needs sorted range)
upper_bound(v.begin(), v.end(), x);     // first element > x
binary_search(v.begin(), v.end(), x);   // returns bool
swap(a, b);
```

---

## 14. Lambdas

```cpp
auto add = [](int a, int b) { return a + b; };
auto add = [&](int a, int b) { return a + b + capturedVar; };  // capture by reference
auto add = [=](int a, int b) { return a + b + capturedVar; };  // capture by value
```
Used constantly for custom sort comparators and priority_queue comparators.

---

## 15. Recursion / Backtracking Skeleton

```cpp
void backtrack(vector<int>& path, vector<int>& nums, vector<vector<int>>& result) {
    if (/* base case */) {
        result.push_back(path);
        return;
    }
    for (int i = 0; i < nums.size(); i++) {
        path.push_back(nums[i]);
        backtrack(path, nums, result);   // recurse
        path.pop_back();                 // undo (backtrack)
    }
}
```

---

## 16. Bit Manipulation

```cpp
x & 1                 // check if odd
x >> 1, x << 1         // divide/multiply by 2
x & (x - 1)            // clear lowest set bit
__builtin_popcount(x)  // count set bits (int)
__builtin_popcountll(x)// count set bits (long long)
1 << k                 // 2^k
x ^ y                  // XOR
```

---

## 17. Passing by Reference (avoid copies / allow mutation)

```cpp
void func(vector<int>& v)         // reference — modifies original, no copy
void func(const vector<int>& v)   // reference, read-only, no copy — use for large inputs you won't modify
void func(vector<int> v)          // by value — makes a copy, wasteful for big containers
```
Rule of thumb: pass containers/strings by `const &` unless you need to modify them, then pass by `&`.

---

## 18. Two Pointers / Sliding Window Skeleton

```cpp
// two pointers (sorted array)
int l = 0, r = v.size() - 1;
while (l < r) {
    if (v[l] + v[r] == target) { ... }
    else if (v[l] + v[r] < target) l++;
    else r--;
}

// sliding window
int l = 0;
for (int r = 0; r < v.size(); r++) {
    // expand window with v[r]
    while (/* window invalid */) {
        // shrink from left
        l++;
    }
    // update answer using window [l, r]
}
```

---

## 19. Graphs (adjacency list + BFS/DFS)

```cpp
vector<vector<int>> adj(n);           // adjacency list
adj[u].push_back(v);                  // add edge u -> v (add both ways if undirected)

// BFS
vector<bool> visited(n, false);
queue<int> q;
q.push(start); visited[start] = true;
while (!q.empty()) {
    int u = q.front(); q.pop();
    for (int v : adj[u]) {
        if (!visited[v]) {
            visited[v] = true;
            q.push(v);
        }
    }
}

// DFS (recursive)
void dfs(int u, vector<vector<int>>& adj, vector<bool>& visited) {
    visited[u] = true;
    for (int v : adj[u]) {
        if (!visited[v]) dfs(v, adj, visited);
    }
}
```

---

## 20. Dynamic Programming Skeleton

```cpp
// 1D DP (e.g. climbing stairs, house robber)
vector<int> dp(n + 1, 0);
dp[0] = base_case;
for (int i = 1; i <= n; i++) {
    dp[i] = /* recurrence using dp[i-1], dp[i-2], etc. */;
}

// 2D DP (e.g. edit distance, LCS)
vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
for (int i = 1; i <= m; i++)
    for (int j = 1; j <= n; j++)
        dp[i][j] = /* recurrence */;
```

---

## Quick Reference: Which Container?

| Need | Use |
|---|---|
| Dynamic array | `vector` |
| Fast key lookup / counting | `unordered_map` |
| Sorted key lookup | `map` |
| Fast membership test, no order | `unordered_set` |
| Sorted unique elements | `set` |
| LIFO | `stack` |
| FIFO | `queue` |
| Both ends | `deque` |
| Kth largest/smallest, always-sorted-min/max | `priority_queue` |

