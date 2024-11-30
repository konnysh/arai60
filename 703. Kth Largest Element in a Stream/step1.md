## Step1(最初のコード)
- 15分くらいで答え見た。以下思考ログ
  - 入力はコピーして内部で持つが、どう持つかはユーザーには影響しないはずなのでソートして持ってもよい。
    - ユーザーがすでにnumsを持っているので、このオブジェクトは入力の配列をそのまま保持しなくていい。
  - ソートだと間に合わない。10^4 でNlogNをN回コールされる。
  - テーマのpriorityqueueは空で書けない。空で書けないデータ構造が必要と思って答え見ることにした。

- k番目をすぐ得られるデータ構造があってそれを知らないのだと思っていたが、答えを見たら上手くmap, priorityqueueなどのデータ構造で工夫しているだけだった。
- データ構造の知識が乏しいのもあって、都合が良いデータ構造を知らないだけと勘違いしがち。もっと工夫をすることを考える。
- 調べてからやりたいので以降はstep2に回す。


```C++
/**
 * Your KthLargest object will be instantiated and called as such:
 * KthLargest* obj = new KthLargest(k, nums);
 * int param_1 = obj->add(val);
 */
 ```

```C++
/*
 * time:15分で答えを見た。
 *
 */
class KthLargest {
public:
    KthLargest(int k, vector<int>& nums) {
        cut_off = k;
        for (int score : nums) {
            scores.push(score);
            if (scores.size() > cut_off) {
                scores.pop();
            }
        }
    }
    
    int add(int val) {
        scores.push(val);
        if (scores.size() > cut_off) {
            scores.pop();
        }
        return scores.top();
    }

private:
    int cut_off;
    std::priority_queue<int, vector<int>, greater<int>> scores;
};

 ```
