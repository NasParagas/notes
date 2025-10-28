---
title: "シミュレーテッド・アニーリング from Scratch"
source: "https://qiita.com/meltyyyyy/items/096efb08fb4ec532c330"
author:
  - "[[meltyyyyy]]"
published: 2022-07-10
created: 2025-08-21
description: "シミュレーテッド・アニーリング(Simulated Annealing、以下SA)は、「焼きなまし法」とも呼ばれ、大域的最適化問題へのアプローチ方法の一つです。「焼きなまし」の名称から察せられる通り、金属を高温の状態にして、徐々に温度を下げることで秩序がある構造を作り出す焼..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

シミュレーテッド・アニーリング(Simulated Annealing、以下SA)は、「焼きなまし法」とも呼ばれ、大域的最適化問題へのアプローチ方法の一つです。「焼きなまし」の名称から察せられる通り、金属を高温の状態にして、徐々に温度を下げることで秩序がある構造を作り出す焼きなましの技術をコンピュータ上で再現したアルゴリズムになります。

最適化問題では、最適解に到達する前に局所解にはまってしまい、大域的な最適解に到達できないケースがしばしば問題になります。下図の例では、 $x=9$ や $x=6$ 付近の局所最適解にはまらずに、 $x=3$ 付近の大域的最適解に到達するのが目標になります。

[![objective.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1530170/1dac41fd-2f72-815c-daee-e38a95bfab20.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2F1dac41fd-2f72-815c-daee-e38a95bfab20.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=270bff813335586f2eaabe4d0df40be4)

この記事では、SAをゼロから実装してみることで、SAに対する理解を深めることを目的とします。

## アルゴリズム

SA法では以下のような手順で探索を行なっていきます。

1. 初期温度を設定する
2. 初期値をサンプリングする
3. 温度を下げる
4. ランダムに次の点をサンプリングする  
	4.1. ランダムにサンプルした点が現在の点よりも良かったら、現在の点を更新する  
	4.2. ランダムにサンプルした点が現在の点よりも悪くても、温度に依存する一定確率で現在の点を更新する
5. 3~4を繰り返す

3の温度を下げる方法は、たとえば次のような関数が使われます。

```python
T = 1.
cooling_rate = 0.9
def cool(T): return cooling_rate * T
T = cool(T)
```

試行回数を増やすたびに温度が下がっていけば問題ないので、次のような方法でもSA法で使うことができます。

```python
T = 1.
n_iter = 100
for iter in range(n_iter):
    T = T / (iter + 1)
```

一つ目の例で、cooling\_rate=0.9, 0.95, 0.99における温度 $T$ のグラフは次のようになります。

[![temprature.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1530170/0ff39049-97a5-7292-3243-1d93be6b969f.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2F0ff39049-97a5-7292-3243-1d93be6b969f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0df9dd364898b5c8f35bf213b7be25c2)

次に4.2の「 ランダムにサンプルした点が現在の点よりも悪くても、温度に依存する一定確率で現在の点を更新する」についてですが、この操作によってSA法は局所解にトラップされるのを回避しています。

単純に現在の解より新しい解の方がよければ更新するだけでは、局所的な解にたどりついてしまうとその後に値を更新することができません。そこで、一定確率で改悪する方向に値を更新することで、局所解からの脱出を可能にします。

しかしながら、常に一定確率で改悪を許してしまうと局所解の中心付近の探索に時間がかかり、計算コストが大きくなってしまいます。SA法では、温度 $T$ に依存する形で、温度 $T$ が下がるにつれて改悪されにくくしていきます。

$$
U(0,1)>exp⁡(−ΔET)
$$

$U(0,1)$ は区間0から1の一様分布で、 $ΔE$ は現在の点と新しい点の差です。式から分かる通り、温度 $T$ が小さくなるにつれて、上式が成り立ちにくくなっていきます。この手法をMetropolis法と呼びます。

cooling\_rate=0.9, 0.95, 0.99における提案された探索点の採択率は次のグラフのようになります。冷却率が低い(cooling\_rate=0.99)場合は、新しい点と現在の点の差分が大きくても採択する確率が他の2つより全体的に高くなっています。また、cooling\_rate=0.9, 0.95, 0.99の全ての場合で冷却が進むにつれて、改悪される確率は低くなっていっています。

[![metropolise.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1530170/cf974736-28ea-1a80-0629-dff82b3ba032.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2Fcf974736-28ea-1a80-0629-dff82b3ba032.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=596bde547c7d2e7b7a3e60c1404538b3)

## 実装

今回最大値を探索する関数には、次の関数を設定します。

$$
f(x)=2sin⁡(x)+3cos⁡(2x)+5sin⁡(23x)
$$

[![objective.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1530170/8764520d-007e-6165-4133-2008ad02c9d6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2F8764520d-007e-6165-4133-2008ad02c9d6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0ab034471e927b19f45f5c6c79c726d7)

SA法で目的関数の最大値を見つけるプログラムを実装します。探索の初期値には、 $x=8$ 付近を設定します。

```python
def objective(x):
    return 2 * np.sin(x) + 3 * np.cos(2 * x) + 5 * np.sin(2 / 3 * x)

n = 10000
data_x = np.linspace(0, 4 * np.pi, n)
data_y = objective(data_x)

def simulated_annealinng(objective, cooling_rate, n_iter):
    x_iter = np.zeros((n_iter, ))
    obj_iter = np.zeros((n_iter, ))

    # set initial temperature and cooling schedule
    T = 1.
    def cool(T): return cooling_rate * T

    # set initial index to 7000,
    # because this point is around minima
    index = 7000
    curr_x = data_x[index]
    curr_obj = objective(curr_x)

    best_x = curr_x
    best_obj = curr_obj

    for i in range(n_iter):

        # decrease T according to cooling schedule
        T = cool(T)

        index = np.random.choice(n, 1)
        new_x = data_x[index]
        new_obj = objective(new_x)

        # update current solution iterate
        if (new_obj > curr_obj) or (np.random.rand()
                                    < np.exp((new_obj - curr_obj) / T)):
            curr_x = new_x
            curr_obj = new_obj

        # Update best solution
        if new_obj > best_obj:
            best_x = new_x
            best_obj = new_obj

        # save solution
        x_iter[i] = best_x
        obj_iter[i] = best_obj

    return x_iter, obj_iter

x_iter, obj_iter = simulated_annealinng(
    objective, cooling_rate=0.9, n_iter=100)
best_index = np.argmax(obj_iter)
print("best x: {}, best objective: {}".format(
    x_iter[best_index], obj_iter[best_index]))
```

[![sa.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2F8c3a69ac-35c5-24b5-351e-9962d2bbc3d3.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=792ca23ff6de58f68a750a982ab06c5c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F1530170%2F8c3a69ac-35c5-24b5-351e-9962d2bbc3d3.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=792ca23ff6de58f68a750a982ab06c5c)

局所解をうまく避けながら、大域的最適解に到達することに成功しています。

プログラムをから察せられる通りSA法の実装は比較的簡単であり、局所解からの脱出が可能であるため、有用なアルゴリズムと言えます。しかしながら、計算量が多く解空間が広くなればなるほど計算コストが肥大化してしまう点や、探索結果がパラメータ（特に冷却率）に大きく依存し、問題設定ごとにパラメータの試行錯誤による調整が必要となるといった問題点が存在しています。

## まとめ

SA法をゼロから実装して、関数の最大値を見つける実験を行いました。

他のベイズ最適化などの最適化アルゴリズムと比べると比較的理解しやすかったです。

今回実装したコードはこちらの [レポジトリ](https://github.com/meltyyyyy/qiita/tree/main/mc/sa) にあります。

[0](https://qiita.com/meltyyyyy/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmeltyyyyy%2Fitems%2F096efb08fb4ec532c330&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmeltyyyyy%2Fitems%2F096efb08fb4ec532c330&realm=qiita)

[22](https://qiita.com/meltyyyyy/items/096efb08fb4ec532c330/likers)

いいねしたユーザー一覧へ移動

15