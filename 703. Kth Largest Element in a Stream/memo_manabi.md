# 703. Kth Largest Element in a Stream
- 問題URL:https://leetcode.com/problems/kth-largest-element-in-a-stream/description/
- 言語：C++

## すでに解いた方々 （inレビュー依頼 問題名で検索）
- https://github.com/haniwachann/leetcode/pull/1
- https://github.com/tarinaihitori/leetcode/pull/8
- https://github.com/hroc135/leetcode/pull/8
- https://github.com/colorbox/leetcode/pull/23
- https://github.com/SuperHotDogCat/coding-interview/pull/38
- https://github.com/ryoooooory/LeetCode/pull/15
- https://github.com/seal-azarashi/leetcode/pull/8
- https://github.com/Yoshiki-Iwasa/Arai60/pull/7
- https://github.com/kazukiii/leetcode/pull/9
- https://github.com/kagetora0924/leetcode-grind/pull/9
- https://github.com/goto-untrapped/Arai60/pull/23
- https://github.com/Ryotaro25/leetcode_first60/pull/9
- https://github.com/Mike0121/LeetCode/pull/19
- https://github.com/fhiyo/leetcode/pull/10
- https://github.com/fhiyo/leetcode/pull/10


### 調べたことなど

- heapの概念を全然知っていなかった。なんかの木、くらいの認識しかなかった。この問題の周辺知識をほとんど知らない感じがする。
  - 「この問題を通して何を知るべきか、するべきか」をまず調べてみる。
    - ヒープとは何か。
      - priority_queue実装出来るレベルでの理解。

    - STL（より一般的に言えば言語の標準ライブラリ）においてヒープを使っているものはあるか？そのリファレンスを目をとおすこと。
      - なんなら実装にまで目をとおしてみる。

    - 木構造、平衡二分木のデータ構造を知る。
    - map, クイックセレクト, multisetによる解答


- ヒープとは何か。
  - ヒープとはおおよそ完全二分木とみなせる配列。（完全k分木とは各nodeに子はk個で葉の深さがすべて同じもの。2^(深さ+1)-1のノード数を持つ）
  ノード数が完全二分木を構成するのに足りなくても、最下位の深さでは左から埋めていく。ノード数をヒープサイズと言い、ヒープサイズ<=配列のサイズとなる。
  - またヒープはヒープ条件(heap property)を満たす。その違いによって二分木には*max-heap*と*min-heap*の二種類が存在する。max-heapでは根が最大値で、nodeの子はnode以下である。min-heapでは根が最小値で、nodeの子はnode以上である。
  - *heapify*の操作によってheap条件を保つ。max-heapfyは、ヒープとノードのindexを入力に取る。index番目のノードが条件を満たしていなければ、そのノードと大きい値を持つの子と値をswapし、スワップ先のノードで再帰的に同じ処理をするということをする。min-heapfyは大小関係が読み替える。計算量はO({木の高さ})あるいはO({ノード数の対数})
    - 高さは葉からの距離、深さは根からの距離、木の高さは根の高さ
  - heapでもなんでもない配列をheapにするには、heapfyをループで呼び出す。入力のindexは（配列のサイズ/2）から1までである。このループは葉に近い側のノードから順に、部分木がヒープ条件を満たすようにしている。
    - ナイーブに考えた計算量はヒープサイズNに対して`O(NlogN)`。しかし数学を頑張るとよりタイトに評価できる。高さhのノード数は高々`n/2^(h+1)`、それぞれが呼ぶheapfyの計算量はO(h)。全ての高さ分でシグマとって全体の計算量は`O(n * Σ\[h=0..log n\]( h/n/2^(h+1) ))`となるが、実は右のいかついやつが等比級数の公式で2で抑えられるので、ヒープの構築にかかる計算量は線形時間`O(N)`。
  - 用途はソートや優先度付きキューの実装など。（ヒープ領域のヒープとは関係ない）

- STLの実装にヒープを使うかは大体決まっているものの、コンパイラによる。
  - ヒープが使われるコンテナはpriority queueで命令は[ここらへん](https://en.cppreference.com/w/cpp/algorithm#Heap_operations)か。
  - C++の内部実装をあたりたかったがどこで見れるのか分からなかった。clangのリポジトリを見てみたが、テンプレートが多用されているのもあって、どこを見ればいいのか、どう見ればいいのか分からなかった。。。
  - Cpythonでよければ[ここらへん](https://github.com/python/cpython/blob/main/Lib/heapq.py)を見る。

- Discordや某ギルドの配布資料、教科書で名前が出たのを確認した、知っていても良さそうな木構造・概念を挙げてみる。名前は見かけたことはあっても、ほとんど全部中身も動作も全然知らなかった。。。 本当に全部が常識に含まれてるかは不明だが、SWEの5割知ってるライン以上ではありそう。
  - 探索木、二分探索木、平衡木、赤黒木、AVL木、トリープ木、2-3木、B木、trie木、patricia木、fenwick木、segment木、Union-Find木、van emde boas木、最小全域木。
  - それぞれの説明は結構長くて大変なので、別のメモファイルにコツコツ追加していくことにする。（赤黒木の理解に時間がかかりすぎて、全部やったらこの問題が終わらない）
  - 参考：[平衡木についての常識の感覚](https://discord.com/channels/1084280443945353267/1183683738635346001/1185264362508795984)
  

- amortized time complexityは償却計算量、償却解析によって求まる計算時間のこと。高価な操作も実際の操作列で考えると、平均してみると一回の操作がそこまでコストが高くないことが分かったりする。
  - 例：スタックを全部popする操作、これを含むn個の操作列の実行時間は最悪でO(n^2)ぽく見えるが、pushする数がnで限りがあるのでO(n)とタイトに評価できる。
  - アルゴリズムイントロダクションだともっと色々書いてある（出納法、ポテンシャル法、動的表）が、これらは分かっていたほうがいいのだろうか。
  - 参考：https://en.wikipedia.org/wiki/Amortized_analysis
  - https://stackoverflow.com/questions/15079327/amortized-complexity-in-laymans-terms

- [スレッドセーフ](https://github.com/fhiyo/leetcode/pull/10#discussion_r1605777064)についての話。C++だと`std::mutex`や`std::lock_guard`でマルチスレッドを制御する。
  - 大学でCUDAを使ったくらいでマルチスレッドプログラミングは全くわからない。。。
