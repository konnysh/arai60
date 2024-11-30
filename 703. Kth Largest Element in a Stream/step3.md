### multisetを使った方法
- 平衡二分探索木を使う方法で、一番自然そうなもの。

```C++
/*
 * Time            :1分45秒
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

 class KthLargest {
  public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (auto num : nums) {
            add(num);
        }
    }
    
    int add(int val) {
        top_k_nums.insert(val);
        if (top_k_nums.size() > capacity) {
            top_k_nums.erase(top_k_nums.begin());
        }
        return *top_k_nums.begin();
    }

  private:
    std::multiset<int> top_k_nums;
    int capacity;
};

 ```

 <br>

### priority_queueを使った方法
- ヒープを使う方法で、一番自然そうなもの。

```C++
/*
 * Time            :1分50秒
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

 class KthLargest {
  public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (auto num : nums) {
            add(num);
        }
    }
    
    int add(int val) {
        top_k_nums.push(val);
        if (top_k_nums.size() > capacity) {
            top_k_nums.pop();
        }
        return top_k_nums.top();
    }

  private:
    std::priority_queue<int, vector<int>, std::greater<int>> top_k_nums;
    int capacity;
};

 ```
