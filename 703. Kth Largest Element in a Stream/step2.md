### mapを使った方法
- この問題はK個の数字を管理してその最小値を取ればよく、最小値を取るやり方には種類がいくつかある。
- mapは最小値を管理できるので、その方法の一つ。priorityqueueより使われるらしい。
- 重複を含む場合mapで管理している本当の数字の数を`.size()`で取得できないので別途管理
- pairなのでしょうがないのかもしれないが、firstが数字を返しているのか一見わかりづらそうなので、intの変数に入れてもよかったのか。。。
- C++のqueueのpop()がvoid型の理由にも関連するが、コンストラクタでaddを呼び出すと返り値のコピーが無駄に発生していて効率は少し悪い。コンパイラがどうにか最適化とかしているかもしれないけれどもそこらへんはわからない。


```C++
/*
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

class KthLargest {
public:
    KthLargest(int k, vector<int> &nums) {
        capacity = k;
        top_k_nums_size = 0;
        for (auto number : nums) {
            add(number);
        }
    }
    
    int add(int val) {
        ++top_k_nums[val];
        ++top_k_nums_size;
        if (top_k_nums_size > capacity) {
            auto min_iter = top_k_nums.begin();
            auto &[key, times] = *min_iter;
            --times;
            if (times == 0) {
                top_k_nums.erase(min_iter);
            }
            --top_k_nums_size;
        }
        return top_k_nums.begin()->first;
    }

private:
    std::map<int, int> top_k_nums;
    int capacity;
    int top_k_nums_size;
};

 ```

<br>

### multisetを使った方法
- mapの方法をよりシンプルにしたという感じ。
- 内部の二分木のノードに重複を許すようにしたのがmultisetで、内部の動きを考えると今回の場合はmapよりかはmultiset使うのが自然そう。


```C++
/*
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

class KthLargest {
public:
    KthLargest(int k, vector<int> &nums) {
        capacity = k;
        for (auto number : nums) {
            add(number);
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

### heap系の関数を使った方法
- [heap系の関数](https://en.cppreference.com/w/cpp/algorithm/make_heap)がvectorをheapにしてくれる。
- std::greater{}はmin_heapにするときに3つ目の引数に渡す。STLでは比較するものの引数としてしばしば使える。C++14から<型>を書く必要が多分なくなった。
- 配列の先頭アクセスをvectorのメンバ関数でなんとなくやってみた。なんか意味あるのかは不明。。。


```C++
/*
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

class KthLargest {
public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (auto number : nums) {
            add(number);
        }
    }
    
    int add(int val) {
        top_k_nums.push_back(val);
        std::push_heap(top_k_nums.begin(), top_k_nums.end(), std::greater{});
        if (top_k_nums.size() > capacity) {
            std::pop_heap(top_k_nums.begin(), top_k_nums.end(), std::greater{});
            top_k_nums.pop_back();
        }
        return top_k_nums.front();
    }

private:
    std::vector<int> top_k_nums;
    int capacity;
};

 ```

 <br>

### クイックセレクトを使った方法(TLE)
- TLEしている。色々実装変えてみたけど、うまくいかず沼にはまりそうだったので切り上げた。
- クイックソートのPartitionをkの方向に向かってやっていく。
- pivot選択には工夫をした方がよい。
  - クイックソートを授業でやったときは、中央値、中央値の中央値、ランダムピボット、配列ランダムシャッフルとかやった。
  - 今回配列の要素の先頭・末尾・真ん中の中央値を取ったけどダメだった

```C++
/*
 * Time Complexity :おそらくO(n^2)
 * Space Complexity:O(n)
 * Auxiliary Space :O(n)
 *
 */

class KthLargest {
public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (auto number : nums) {
            add(number);
        }
    }
    
    int add(int val) {
        top_k_nums.push_back(val);
        int left = 0;
        int right = top_k_nums.size() - 1;
        while (left <= right) {
            if (left == right) {
                return top_k_nums[left];
            }
            int pivot = Partition(left, right);
            if (pivot == capacity - 1) {
                return top_k_nums[pivot];
            }
            if (pivot < capacity-1) {
                left = std::min(pivot + 1, right);
                continue;
            }
            right = std::max(pivot - 1, left);
        }
    }

private:
    std::vector<int> top_k_nums;
    int capacity;

    int Partition(const int &left, const int &right) {
        int pivot = GetMedianPivotIndex(left, right);
        std::swap(top_k_nums[pivot], top_k_nums[right]);
        pivot = right;
        int bound = left;
        for (int i = left; i < pivot; ++i) {
            if (top_k_nums[i] > top_k_nums[pivot]) {
                std::swap(top_k_nums[i], top_k_nums[bound]);
                ++bound;
            }
        }
        std::swap(top_k_nums[pivot], top_k_nums[bound]);
        return bound;
    }

    int GetMedianPivotIndex(const int &left, const int &right) {
        const int mid = left + (right - left) / 2;
        const int left_val = top_k_nums[left];
        const int mid_val = top_k_nums[mid];
        const int right_val = top_k_nums[right];
        if ((left_val <= mid_val && mid_val <= right_val) || (right_val <= mid_val && mid_val <= left_val)) {
            return mid;
        } else if ((mid_val <= left_val && left_val <= right_val) || (right_val <= left_val && left_val <= mid_val)) {
            return left;
        } else {
            return right;
        }
    }
};

 ```

 <br>

 ### priority_queueを使った方法
- step1の改善版みたいなコード。変数名を変えたのととコンストラクタでもaddを使うようにした。

```C++
/*
 * Time Complexity :O(nlogk)
 * Space Complexity:O(n)
 * Auxiliary Space :O(k)
 *
 */

class KthLargest {
public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (int num : nums) {
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
    int capacity;
    std::priority_queue<int, vector<int>, std::greater<int>> top_k_nums;
};


 ```

 <br>

 ### 自作priority_queueを使った方法
- min_heapの自作をした。
  - min_heapに外部から求めるのは、size, empty, top, push, pop
  - 内部的にはheapを持つvectorと、heapifyするコンストラクタも欲しい。
    - 実装していたら色々な関数が必要になって、それも書いた。

- テンプレートにできたらクラスの汎用性が上がるが、これ以上時間かけてしまうと全然進まないので今回は一旦このくらいで。。。


#### 自作priority_queue

```C++
class IntMinHeap {
  public:
    IntMinHeap() {}
    IntMinHeap(vector<int> v);

    int size() {
        return heap.size();   
    }

    bool empty() {
        return heap.empty();
    }

    int top() {
        return heap.front();
    }

    void push(const int val);

    void pop();

  private:
    vector<int> heap;

    int GetLeft(const int index) {
        return 2 * index + 1;
    }

    int GetRight(const int index) {
        return 2 * index + 2;
    }

    int GetParent(const int index) {
        return (index - 1) / 2;
    }

    bool HasParent(const int index) {
        return index != 0;
    }

    bool HasLeft(const int index) {
        return GetLeft(index) < heap.size();
    }

    bool HasRight(const int index) {
        return GetRight(index) < heap.size();
    }

    void MinHeapify(const int index);
};

IntMinHeap::IntMinHeap(vector<int> v) : heap(v) {
    for (int index = GetParent(heap.size() - 1); index >= 0; --index) {
        MinHeapify(index);
    }
}

void IntMinHeap::MinHeapify(const int index) {
    int smallest_number = heap[index];
    int smallest_number_index = index;
    if (HasLeft(index) && heap[GetLeft(index)] < smallest_number) {
        smallest_number = heap[GetLeft(index)];
        smallest_number_index = GetLeft(index);
    }
    if (HasRight(index) && heap[GetRight(index)] < smallest_number) {
        smallest_number = heap[GetRight(index)];
        smallest_number_index = GetRight(index);
    }

    if (index != smallest_number_index) {
        std::swap(heap[index], heap[smallest_number_index]);
        MinHeapify(smallest_number_index);
    }
}

void IntMinHeap::push(const int val) {
    heap.push_back(val);
    int index = heap.size() - 1;
    while (true) {
        if (!HasParent(index)) {
            return;
        }
        int parent = GetParent(index);
        if (heap[index] < heap[parent]) {
            std::swap(heap[index], heap[parent]);
            index = parent;
            continue;
        }
        return;
    }
}

void IntMinHeap::pop() {
    heap.front() = heap.back();
    heap.pop_back();
    MinHeapify(0);
}

```


#### コード

```C++
class KthLargest {
public:
    KthLargest(int k, vector<int>& nums) {
        capacity = k;
        for (int num : nums) {
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
    int capacity;
    IntMinHeap top_k_nums;
};

```
