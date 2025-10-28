---
title: "Dynamic Window Approachを実装してみた【経路計画アルゴリズム】【自律移動ロボットアルゴリズム】"
source: "https://qiita.com/MENDY/items/c96e112d6fa0d7e29029"
author:
  - "[[MENDY]]"
published: 2018-08-10
created: 2025-08-26
description: "はじめに この夏で興味のあることはぜんぶやろうときめたのでその3つめ（経路計画）（1つ目は強化学習, 2つ目はRNN(LSTM)） RRT 基本編 ゴール・障害物回避編 実践編 DWA 理論編⇒後で自分で読んでみたらすごくわかりにくいです．．．ごめんなさい，時..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## はじめに

この夏で興味のあることはぜんぶやろうときめたのでその3つめ（経路計画）（1つ目は [強化学習](https://qiita.com/MENDY/items/77608bb0561c4630d971), 2つ目はRNN(LSTM)）

- RRT
	- [基本編](https://qiita.com/MENDY/items/176cc16c746dc6135ef4)
	- [ゴール・障害物回避編](https://qiita.com/MENDY/items/23950fb3f32dbb5e4886)
	- [実践編](https://qiita.com/MENDY/items/cbae8a8d338690ea0c9e)
- DWA
	- [理論編](https://qiita.com/MENDY/items/16343a00d37d14234437) ⇒後で自分で読んでみたらすごくわかりにくいです．．．ごめんなさい，時間があったら書き直します
	- [実装編](https://qiita.com/MENDY/items/c96e112d6fa0d7e29029)
- A\*

などを実装していく予定です

github:[https://github.com/Shunichi09](https://github.com/Shunichi09)

## 参考

少し苦戦したので，ロボット界で有名なエニグマさん  
[https://myenigma.hatenablog.com/entry/20140624/1403618922](https://myenigma.hatenablog.com/entry/20140624/1403618922)  
を参考にしました

## 環境

- python3.6.6
- Windows 10
- anaconda
- matplotlib 2.2.2
- numpy 1.14.3

## DWAって結局なんだっけ

ダイナミックウィンドウアプローチという名前がフルネームです  
よくロボットの制御に用いられています  
特に対向二輪，差動二輪と呼ばれるロボットですね  
こいつの制御は非ホロノミック制約なので，非常に厄介なのです  
（制御的には，大域的な安定解を持たないという非常にめんどくさい性質を持ちます．みんなの味方の最適レギュレータ理論，LQRが使えません！詳しくは，Brocketの定理で証明ができます）が，このDWAはすんなりそれを解決するという優れもの

上の説明からも分かるように理論ベースというよりアルゴリズムベースです  
イメージは，自分の行ける範囲から選んじゃおうという発想  
人間っぽいのかな？

やってみたらの動画を先に見せます  
★がゴールです

[![basic_animation.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2F67da3df9-3309-e2f6-fef9-56e9327ea2f2.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e96e8d6f52d1613c941a5782d2e17bf5)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2F67da3df9-3309-e2f6-fef9-56e9327ea2f2.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e96e8d6f52d1613c941a5782d2e17bf5)

障害物を交わしながらゴールに向かって言っている様子がわかるかと思います

## 実装について

ROSのパッケージにも入ってしまっているので自分でいじることは少ないと思いますが，どうせなら手で書いてみようってことでやってます  
後でいろいろいじれるし

## アルゴリズム

前回の理論編である程度は説明したので，今回はおさらいとプログラムで書くとどうなるかを説明します．  
絵はあんまりありません

DWAは以下の流れで入力が決定します

1. 取りえる $(v,ω)$ の組み合わせを決定
2. 予測軌跡を作成
3. 評価関数に代入し，最もよい軌跡を生み出した $(v,ω)$ を選出
4. 選出した $(v,ω)$ をロボットに代入
5. ゴールにつくまで繰り返す

[![image.png](https://qiita-image-store.s3.amazonaws.com/0/261584/f40e719d-cb64-315d-a275-781fcea3a2c0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Ff40e719d-cb64-315d-a275-781fcea3a2c0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0bdcb08eed89cacbc75b5f808c97e09d)

さっきの図でいうとこんな感じですね  
候補を作ってそれを選んでいきます

では，さっそく説明していきます

## 取りえる速度と角速度の組み合わせを決定

これは，ロボットの性能から決定します  
今現在の速度から最高性能をだしたときの加速度を計算すればよいですね

それを細かく分割して組み合わせを作ります  
分解能は細かくてもいいと思いますがその分計算時間がとてつもなく増えるので注意してください

プログラムではこんな感じ

```py
def _calc_range_velos(self, state): # 角速度と角度の範囲決定①
        # 角速度
        range_ang_velo = self.samplingtime * self.simu_robot.max_ang_accelation
        min_ang_velo = state.u_th - range_ang_velo
        max_ang_velo = state.u_th + range_ang_velo
        # 最小値
        if min_ang_velo < self.simu_robot.lim_min_ang_velo:
            min_ang_velo = self.simu_robot.lim_min_ang_velo
        # 最大値
        if max_ang_velo > self.simu_robot.lim_max_ang_velo:
            max_ang_velo = self.simu_robot.lim_max_ang_velo

        # 速度
        range_velo = self.samplingtime * self.simu_robot.max_accelation
        min_velo = state.u_v - range_velo
        max_velo = state.u_v + range_velo
        # 最小値
        if min_velo < self.simu_robot.lim_min_velo:
            min_velo = self.simu_robot.lim_min_velo
        # 最大値
        if max_velo > self.simu_robot.lim_max_velo:
            max_velo = self.simu_robot.lim_max_velo

        return min_ang_velo, max_ang_velo, min_velo, max_velo
```

レンジ，取りえる最大速度と最小速度を計算している様子がわかるかと思います  
これをもとに予測軌跡を引きます

## 予測軌跡

これは単純にrobotのモデルに合わせて回すだけです  
予測時間を設定して，回してください  
ただ，これも計算時間が増えるのでパラメータ設定は慎重に

```py
def _make_path(self, state): 
        # 角度と速度の範囲算出
        min_ang_velo, max_ang_velo, min_velo, max_velo = self._calc_range_velos(state)

        # 全てのpathのリスト
        paths = []

        # 角速度と速度の組み合わせを全探索
        for ang_velo in np.arange(min_ang_velo, max_ang_velo, self.delta_ang_velo):
            for velo in np.arange(min_velo, max_velo, self.delta_velo):

                path = Path(ang_velo, velo)

                next_x, next_y, next_th \
                    = self.simu_robot.predict_state(ang_velo, velo, state.x, state.y, state.th, self.samplingtime, self.pre_step)

                path.x = next_x
                path.y = next_y
                path.th = next_th
            
                # 作ったpathを追加
                paths.append(path)
        
        # 時刻歴Pathを保存
        self.traj_paths.append(paths)

        return paths
```

## 軌跡の評価

では，引いた軌跡を評価します

### まず，ゴールの方に向いてるか

これは引いたPathの最終時刻がこっちに向いてればいいので，調べるの簡単です  
（小さければ小さいほどよいので180から引いて逆転させてます⇒大きければ大きいほどよい）

```py
def _heading_angle(self, path, g_x, g_y): # ゴールに向いているか
        # 終端の向き
        last_x = path.x[-1]
        last_y = path.y[-1]
        last_th = path.th[-1]

        # 角度計算
        angle_to_goal = math.atan2(g_y-last_y, g_x-last_x)

        # score計算
        score_angle = angle_to_goal - last_th

        # ぐるぐる防止
        score_angle = abs(angle_range_corrector(score_angle))
        
        # 最大と最小をひっくり返す
        score_angle = math.pi - score_angle

        # print('score_sngle = {0}' .format(score_angle))

        return score_angle
```

### 次に，並進の速度がでているかをみます

並進の速度がでていれば進んでいることになるので，ゴールに近づいてることになります  
（大きければ大きいほどよい）  
すっごく簡単です  
Pathクラスに引いたときの速度と角速度を保存してあるので，呼び出して

```py
def _heading_velo(self, path): # 速く進んでいるか（直進）

        score_heading_velo = path.u_v

        return score_heading_velo
```

さて，次からが論文と少し異なるところです．  
論文では，

### 障害物をみます

障害物をみるときに最も近い障害物をみて，それとの距離を算出し，不必要なPathを次式で消してます

[![image.png](https://qiita-image-store.s3.amazonaws.com/0/261584/ad899258-0961-5cc9-7987-c2bb6baaf33a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Fad899258-0961-5cc9-7987-c2bb6baaf33a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8979b064b16e5f60662d200168c7daf7)

論文より引用しています  
([https://ieeexplore.ieee.org/document/580977/](https://ieeexplore.ieee.org/document/580977/))

これは，あの高校の時にやった物理の方程式

$$
v=2gh
$$

を使ってますが，ここでは使いません．．  
レーザーの場合は実際の障害物の大きさが分からないのでこうして，距離から速度を算出しているのだと思いますが，今回は既知でいきます

なのでまず，  
自分の周囲何メートルかにいる障害物を障害物認定します（この場合は5m）

```py
def _calc_nearest_obs(self, state, obstacles):
        area_dis_to_obs = 5 # パラメータ（何メートル考慮するか，本当は制動距離）
        nearest_obs = [] # あるエリアに入ってる障害物

        for obs in obstacles:
            temp_dis_to_obs = math.sqrt((state.x - obs.x) ** 2 + (state.y - obs.y) ** 2)

            if temp_dis_to_obs < area_dis_to_obs :
                nearest_obs.append(obs)

        return nearest_obs
```

認定した障害物との距離を計算します  
そのとき経路の中でもっとも近づいたときの値を使います  
（大きければ大きいほど良い）  
下の図で右の障害物ともっとも近くなるのは，赤い予測軌道であれば，オレンジでしめしたところですね

[![image.png](https://qiita-image-store.s3.amazonaws.com/0/261584/cea0bfec-38cd-af1b-35f0-41aff3e01d08.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Fcea0bfec-38cd-af1b-35f0-41aff3e01d08.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c25693cf65bf9d24893a9675d3266cb2)

```py
def _obstacle(self, path, nearest_obs):
        # 障害物回避（エリアに入ったらその線は使わない）/ (障害物ともっとも近い距離距離)))
        score_obstacle = 2
        temp_dis_to_obs = 0.0

        for i in range(len(path.x)):
            for obs in nearest_obs: 
                temp_dis_to_obs = math.sqrt((path.x[i] - obs.x) * (path.x[i] - obs.x) + (path.y[i] - obs.y) *  (path.y[i] - obs.y))
                
                if temp_dis_to_obs < score_obstacle:
                    score_obstacle = temp_dis_to_obs # 一番近いところ

                # そもそも中に入ってる判定
                if temp_dis_to_obs < obs.size + 0.75: # マージン
                    score_obstacle = -float('inf')
                    break
            
            else:
                continue
            
            break

        return score_obstacle
```

ここでは，障害物との距離が一定値より小さくなった場合はその予測軌跡を，scoreを-infにすることで消去してます  
また，2m遠かったらそれ以上遠くても評価する必要がないので，2mでとめてます

## 評価

最後に今までのスコアを評価しますが，ここで大事なのは，各スコアの重みづけです  
正直，ロボットの性能と障害物との環境に激しく依存するので，どうにかこうにかやってみてください

また，評価する際に，すべてを正規化することを忘れないようにお願いします  
正規化しないと，結構大変なことになります

評価値を比較して  
**最も大きいものを選びます！！！**

```py
def _eval_path(self, paths, g_x, g_y, state, obastacles):
        # 一番近い障害物判定
        nearest_obs = self._calc_nearest_obs(state, obastacles)

        score_heading_angles = []
        score_heading_velos = []
        score_obstacles = []

        # 全てのpathで評価を検索
        for path in paths:
            # (1) heading_angle
            score_heading_angles.append(self._heading_angle(path, g_x, g_y))
            # (2) heading_velo
            score_heading_velos.append(self._heading_velo(path))
            # (3) obstacle
            score_obstacles.append(self._obstacle(path, nearest_obs))

        # print('angle = {0}'.format(score_heading_angles))
        # print('velo = {0}'.format(score_heading_velos))
        # print('obs = {0}'.format(score_obstacles))

        # 正規化
        for scores in [score_heading_angles, score_heading_velos, score_obstacles]:
            scores = min_max_normalize(scores)

        score = 0.0
        # 最小pathを探索
        for k in range(len(paths)):
            temp_score = 0.0

            temp_score = self.weight_angle * score_heading_angles[k] + \
                         self.weight_velo * score_heading_velos[k] + \
                         self.weight_obs * score_obstacles[k]
        
            if temp_score > score:
                opt_path = paths[k]
                score = temp_score
                
        return opt_path
```

これで，できました  
あとはこれを繰り返すだけです  
全部のまとめと，アニメーションのプログラムも下に添付しておきます

## いろんなケースで試してみる

### ゴールをうごかしてもうまくいく

[![basic_animation.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Ffc1bcc6d-1cb1-9589-0039-f5e5310a593d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bda086da605ca1678b564ce80f73a179)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Ffc1bcc6d-1cb1-9589-0039-f5e5310a593d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bda086da605ca1678b564ce80f73a179)

### 障害物のパラメータを大きくする）

⇒いったんいくところがなくなりかけるけど，突破口をみつけると加速する

[![basic_animation.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Fce46d089-f1b2-2ea3-1f86-664a8d2c52fb.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1af7416c6078020d06c85f5169bec6cd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2Fce46d089-f1b2-2ea3-1f86-664a8d2c52fb.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1af7416c6078020d06c85f5169bec6cd)

### 角度のパラメータを大きくする

⇒局所解に陥る  
ゴールの方にいきたいんだけど，障害物が邪魔でってなっている

最後はゴールの位置が変わって向かっていけてる

[![basic_animation.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2F6957c528-c5e9-d8a4-d7c6-a7f01113df45.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7b6ead59cc6f274435ee96e4eaeb5224)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F261584%2F6957c528-c5e9-d8a4-d7c6-a7f01113df45.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7b6ead59cc6f274435ee96e4eaeb5224)

## 課題

やはり，パラメータ設定が難しいです  
角度のパラメータを小さくしすぎるとゴールに行かないし  
障害物を大きくすると動けなくなります

パラメータAutoでかえるとか（周囲環境見て）  
動けなくなるのをさけるにはやっぱりRRTとかと併用ですかね～  
また時間があればやります！

[強化学習](https://qiita.com/MENDY/items/77608bb0561c4630d971) の第五回もそろそろあげます

## プログラム

```py
# controller
import numpy as np
np.seterr(divide='ignore', invalid='ignore')

import matplotlib.pyplot as plt
import pandas as pd
from animation import Animation_robot
import math
import sys

# 基本関数
# 正規化
def min_max_normalize(data):

    data = np.array(data)
    
    max_data = max(data)
    min_data = min(data)

    if max_data - min_data == 0:
        data = [0.0 for i in range(len(data))]
    else:
        data = (data - min_data) / (max_data - min_data)

    return data
# 角度補正用
def angle_range_corrector(angle):

    if angle > math.pi:
        while angle > math.pi:
            angle -=  2 * math.pi
    elif angle < -math.pi:
        while angle < -math.pi:
            angle += 2 * math.pi
    
    return angle

# 円を書く
def write_circle(center_x, center_y, angle, circle_size=0.2):#人の大きさは半径15cm
    # 初期化
    circle_x = [] #位置を表す円のx
    circle_y = [] #位置を表す円のy

    steps = 100 #円を書く分解能はこの程度で大丈夫
    for i in range(steps):
        circle_x.append(center_x + circle_size*math.cos(i*2*math.pi/steps))
        circle_y.append(center_y + circle_size*math.sin(i*2*math.pi/steps))
    
    circle_line_x = [center_x, center_x + math.cos(angle) * circle_size]
    circle_line_y = [center_y, center_y + math.sin(angle) * circle_size]
    
    return circle_x, circle_y, circle_line_x, circle_line_y

# ルール
# x, y, thは基本的に今のstate
# g_ はgoal
# traj_ は過去の軌跡
# 単位は，角度はrad，位置はm
# 二輪モデルなので入力は速度と角速度

# path
class Path():
    def __init__(self, u_th, u_v): 
        self.x = None
        self.y = None
        self.th = None
        self.u_v = u_v
        self.u_th = u_th

class Obstacle():
    def __init__(self, x, y, size):
        self.x = x
        self.y = y
        self.size = size
        
class Two_wheeled_robot(): # 実際のロボット
    def __init__(self, init_x, init_y, init_th):
        # 初期状態
        self.x = init_x
        self.y = init_y
        self.th = init_th
        self.u_v = 0.0
        self.u_th = 0.0

        # 時刻歴保存用
        self.traj_x = [init_x]
        self.traj_y = [init_y]
        self.traj_th = [init_th]
        self.traj_u_v = [0.0]
        self.traj_u_th = [0.0]

    def update_state(self, u_th, u_v, dt): # stateを更新

        self.u_th = u_th
        self.u_v = u_v
        
        next_x = self.u_v * math.cos(self.th) * dt + self.x
        next_y = self.u_v * math.sin(self.th) * dt + self.y
        next_th = self.u_th * dt + self.th

        self.traj_x.append(next_x)
        self.traj_y.append(next_y)
        self.traj_th.append(next_th)

        self.x = next_x
        self.y = next_y
        self.th = next_th

        return self.x, self.y, self.th # stateを更新

class Simulator_DWA_robot(): # DWAのシミュレータ用
    def __init__(self):
        # self.model 独立二輪型
        # 加速度制限
        self.max_accelation = 1.0
        self.max_ang_accelation = 100 * math.pi /180
        # 速度制限
        self.lim_max_velo = 1.6 # m/s
        self.lim_min_velo = 0.0 # m/s
        self.lim_max_ang_velo = math.pi
        self.lim_min_ang_velo = -math.pi

    # 予想状態を作成する
    def predict_state(self, ang_velo, velo, x, y, th, dt, pre_step): # DWA用(何秒後かのstateを予測))
        next_xs = []
        next_ys = []
        next_ths = []

        for i in range(pre_step):
            temp_x = velo * math.cos(th) * dt + x
            temp_y = velo * math.sin(th) * dt + y
            temp_th = ang_velo * dt + th

            next_xs.append(temp_x)
            next_ys.append(temp_y)
            next_ths.append(temp_th)

            x = temp_x
            y = temp_y
            th = temp_th

        # print('next_xs = {0}'.format(next_xs))

        return next_xs, next_ys, next_ths # 予想した軌跡

# DWA
class DWA():
    def __init__(self):
        # 初期化
        # simulation用のロボット
        self.simu_robot = Simulator_DWA_robot()

        # 予測時間(s)
        self.pre_time = 3
        self.pre_step = 30

        # 探索時の刻み幅
        self.delta_velo = 0.02
        self.delta_ang_velo = 0.02

        # サンプリングタイム(変更の場合，共通する項があるので，必ずほかのところも確認する)
        self.samplingtime = 0.1

        # 重みづけ
        self.weight_angle = 0.04
        self.weight_velo = 0.2
        self.weight_obs = 0.1

        # すべてのPathを保存
        self.traj_paths = []
        self.traj_opt = []

    def calc_input(self, g_x, g_y, state, obstacles): # stateはロボットクラスでくる
        # Path作成
        paths = self._make_path(state)
        # Path評価
        opt_path = self._eval_path(paths, g_x, g_y, state, obstacles)

        self.traj_opt.append(opt_path)
        
        return paths, opt_path

    def _make_path(self, state): 
        # 角度と速度の範囲算出
        min_ang_velo, max_ang_velo, min_velo, max_velo = self._calc_range_velos(state)

        # 全てのpathのリスト
        paths = []

        # 角速度と速度の組み合わせを全探索
        for ang_velo in np.arange(min_ang_velo, max_ang_velo, self.delta_ang_velo):
            for velo in np.arange(min_velo, max_velo, self.delta_velo):

                path = Path(ang_velo, velo)

                next_x, next_y, next_th \
                    = self.simu_robot.predict_state(ang_velo, velo, state.x, state.y, state.th, self.samplingtime, self.pre_step)

                path.x = next_x
                path.y = next_y
                path.th = next_th
            
                # 作ったpathを追加
                paths.append(path)
        
        # 時刻歴Pathを保存
        self.traj_paths.append(paths)

        return paths

    def _calc_range_velos(self, state): # 角速度と角度の範囲決定①
        # 角速度
        range_ang_velo = self.samplingtime * self.simu_robot.max_ang_accelation
        min_ang_velo = state.u_th - range_ang_velo
        max_ang_velo = state.u_th + range_ang_velo
        # 最小値
        if min_ang_velo < self.simu_robot.lim_min_ang_velo:
            min_ang_velo = self.simu_robot.lim_min_ang_velo
        # 最大値
        if max_ang_velo > self.simu_robot.lim_max_ang_velo:
            max_ang_velo = self.simu_robot.lim_max_ang_velo

        # 速度
        range_velo = self.samplingtime * self.simu_robot.max_accelation
        min_velo = state.u_v - range_velo
        max_velo = state.u_v + range_velo
        # 最小値
        if min_velo < self.simu_robot.lim_min_velo:
            min_velo = self.simu_robot.lim_min_velo
        # 最大値
        if max_velo > self.simu_robot.lim_max_velo:
            max_velo = self.simu_robot.lim_max_velo

        return min_ang_velo, max_ang_velo, min_velo, max_velo

    def _eval_path(self, paths, g_x, g_y, state, obastacles):
        # 一番近い障害物判定
        nearest_obs = self._calc_nearest_obs(state, obastacles)

        score_heading_angles = []
        score_heading_velos = []
        score_obstacles = []

        # 全てのpathで評価を検索
        for path in paths:
            # (1) heading_angle
            score_heading_angles.append(self._heading_angle(path, g_x, g_y))
            # (2) heading_velo
            score_heading_velos.append(self._heading_velo(path))
            # (3) obstacle
            score_obstacles.append(self._obstacle(path, nearest_obs))

        # print('angle = {0}'.format(score_heading_angles))
        # print('velo = {0}'.format(score_heading_velos))
        # print('obs = {0}'.format(score_obstacles))

        # 正規化
        for scores in [score_heading_angles, score_heading_velos, score_obstacles]:
            scores = min_max_normalize(scores)

        score = 0.0
        # 最小pathを探索
        for k in range(len(paths)):
            temp_score = 0.0

            temp_score = self.weight_angle * score_heading_angles[k] + \
                         self.weight_velo * score_heading_velos[k] + \
                         self.weight_obs * score_obstacles[k]
        
            if temp_score > score:
                opt_path = paths[k]
                score = temp_score
                
        return opt_path

    def _heading_angle(self, path, g_x, g_y): # ゴールに向いているか
        # 終端の向き
        last_x = path.x[-1]
        last_y = path.y[-1]
        last_th = path.th[-1]

        # 角度計算
        angle_to_goal = math.atan2(g_y-last_y, g_x-last_x)

        # score計算
        score_angle = angle_to_goal - last_th

        # ぐるぐる防止
        score_angle = abs(angle_range_corrector(score_angle))
        
        # 最大と最小をひっくり返す
        score_angle = math.pi - score_angle

        # print('score_sngle = {0}' .format(score_angle))

        return score_angle

    def _heading_velo(self, path): # 速く進んでいるか（直進）

        score_heading_velo = path.u_v

        return score_heading_velo

    def _calc_nearest_obs(self, state, obstacles):
        area_dis_to_obs = 5 # パラメータ（何メートル考慮するか，本当は制動距離）
        nearest_obs = [] # あるエリアに入ってる障害物

        for obs in obstacles:
            temp_dis_to_obs = math.sqrt((state.x - obs.x) ** 2 + (state.y - obs.y) ** 2)

            if temp_dis_to_obs < area_dis_to_obs :
                nearest_obs.append(obs)

        return nearest_obs

    def _obstacle(self, path, nearest_obs):
        # 障害物回避（エリアに入ったらその線は使わない）/ (障害物ともっとも近い距離距離)))
        score_obstacle = 2
        temp_dis_to_obs = 0.0

        for i in range(len(path.x)):
            for obs in nearest_obs: 
                temp_dis_to_obs = math.sqrt((path.x[i] - obs.x) * (path.x[i] - obs.x) + (path.y[i] - obs.y) *  (path.y[i] - obs.y))
                
                if temp_dis_to_obs < score_obstacle:
                    score_obstacle = temp_dis_to_obs # 一番近いところ

                # そもそも中に入ってる判定
                if temp_dis_to_obs < obs.size + 0.75: # マージン
                    score_obstacle = -float('inf')
                    break
            
            else:
                continue
            
            break

        return score_obstacle

class Const_goal():# goal作成プログラム
    def __init__(self):
        # self.human_trajectory = ...的な
        self.traj_g_x = []
        self.traj_g_y = []

    def calc_goal(self, time_step): # 本当は人の値が入ってもよいかも
        if time_step <= 100:
            g_x  = 10.0
            g_y = 10.0
        else:
            g_x = -10.0
            g_y = -10.0

        self.traj_g_x.append(g_x)
        self.traj_g_y.append(g_y)

        return g_x, g_y

class Main_controller():# Mainの制御クラス
    def __init__(self):
        self.robot = Two_wheeled_robot(0.0, 0.0, 0.0)
        self.goal_maker = Const_goal()
        self.controller = DWA()

        # 障害物（本当はレーザーの値等を使用）
        self.obstacles = []
        '''
        obstacle_num = 3
        for i in range(obstacle_num):
            # x = np.random.randint(-5, 5)
            # y = np.random.randint(-5, 5)
            # size = np.random.randint(-5, 5)

            self.obstacles.append(Obstacle(x, y, size))
        '''

        self.obstacles =[Obstacle(4, 1, 0.25), Obstacle(0, 4.5, 0.25),  Obstacle(3, 4.5, 0.25), Obstacle(5, 3.5, 0.25),  Obstacle(7.5, 9.0, 0.25)]

        # ここを変えたら他もチェック
        self.samplingtime = 0.1

    def run_to_goal(self):
        goal_flag = False
        time_step = 0

        while not goal_flag:
        # for i in range(250):
            g_x, g_y = self.goal_maker.calc_goal(time_step)

            # 入力決定
            paths, opt_path = self.controller.calc_input(g_x, g_y, self.robot, self.obstacles)

            u_th = opt_path.u_th
            u_v = opt_path.u_v
            
            # 入力で状態更新
            self.robot.update_state(u_th, u_v, self.samplingtime)

            # goal判定
            dis_to_goal = np.sqrt((g_x-self.robot.x)*(g_x-self.robot.x) + (g_y-self.robot.y)*(g_y-self.robot.y))
            if dis_to_goal < 0.5:
                goal_flag = True
            
            time_step += 1

        return self.robot.traj_x, self.robot.traj_y, self.robot.traj_th, \
                self.goal_maker.traj_g_x, self.goal_maker.traj_g_y, self.controller.traj_paths, self.controller.traj_opt, self.obstacles

def main():
    animation = Animation_robot()
    animation.fig_set()

    controller = Main_controller()
    traj_x, traj_y, traj_th, traj_g_x, traj_g_y, traj_paths, traj_opt, obstacles = controller.run_to_goal()

    ani = animation.func_anim_plot(traj_x, traj_y, traj_th, traj_paths, traj_g_x, traj_g_y, traj_opt, obstacles)

if __name__ == '__main__':
    main()
```

```py
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.animation as ani
import math
import sys

# 円を書く
def write_circle(center_x, center_y, angle, circle_size=0.2):#人の大きさは半径15cm
    # 初期化
    circle_x = [] #位置を表す円のx
    circle_y = [] #位置を表す円のy

    steps = 100 #円を書く分解能はこの程度で大丈夫
    for i in range(steps):
        circle_x.append(center_x + circle_size*math.cos(i*2*math.pi/steps))
        circle_y.append(center_y + circle_size*math.sin(i*2*math.pi/steps))
    
    circle_line_x = [center_x, center_x + math.cos(angle) * circle_size]
    circle_line_y = [center_y, center_y + math.sin(angle) * circle_size]
    
    return circle_x, circle_y, circle_line_x, circle_line_y

class Path_anim():
    def __init__(self, axis):
        self.path_img, = axis.plot([], [], color='c', linestyle='dashed', linewidth=0.15)

    def set_graph_data(self, x, y):
        self.path_img.set_data(x, y)

        return self.path_img, 
    
class Obstacle_anim():
    def __init__(self, axis):
        self.obs_img, = axis.plot([], [], color='k')

    def set_graph_data(self, obstacle):
        angle = 0.0
        circle_x, circle_y, circle_line_x, circle_line_y = \
                write_circle(obstacle.x, obstacle.y, angle, circle_size=obstacle.size)

        self.obs_img.set_data(circle_x, circle_y)

        return self.obs_img, 

class Animation_robot():
    def __init__(self):
        self.fig = plt.figure()
        self.axis = self.fig.add_subplot(111)

    def fig_set(self):
        # 初期設定 軸
        MAX_x = 12
        min_x = -12
        MAX_y = 12
        min_y = -12

        self.axis.set_xlim(min_x, MAX_x)
        self.axis.set_ylim(min_y, MAX_y)

        # 軸
        self.axis.grid(True)

        # 縦横比
        self.axis.set_aspect('equal')

        # label
        self.axis.set_xlabel('X [m]')
        self.axis.set_ylabel('Y [m]')

    def plot(self, traj_x, traj_y): # ただのplot
        self.axis.plot(traj_x, traj_y)

        plt.show()
    
    def func_anim_plot(self, traj_x, traj_y, traj_th, traj_paths, traj_g_x, traj_g_y, traj_opt, obstacles):
        # selfにしておく
        self.traj_x = traj_x
        self.traj_y = traj_y
        self.traj_th = traj_th
        self.traj_paths = traj_paths
        self.traj_g_x = traj_g_x
        self.traj_g_y = traj_g_y
        self.traj_opt = traj_opt
        self.obstacles = obstacles

        # trajお絵かき
        self.traj_img, = self.axis.plot([], [], 'k', linestyle='dashed')

        # 円と向き
        self.robot_img, = self.axis.plot([], [], 'k')

        self.robot_angle_img, = self.axis.plot([], [], 'k')

        # goalを追加
        self.img_goal, = self.axis.plot([], [], '*', color='b', markersize=15)

        # dwa # 何本線引くかは考える
        self.dwa_paths = []
        self.max_path_num = 100
        for k in range(self.max_path_num):
            self.dwa_paths.append(Path_anim(self.axis))

        # opt_traj
        self.traj_opt_img, = self.axis.plot([], [], 'r', linestyle='dashed')

        # 障害物
        self.obs = []
        self.obstacles_num = len(obstacles)
        for k in range(len(obstacles)):
            self.obs.append(Obstacle_anim(self.axis))
        
        # ステップ数表示
        self.step_text = self.axis.text(0.05, 0.9, '', transform=self.axis.transAxes)

        animation = ani.FuncAnimation(self.fig, self._update_anim, interval=100, \
                              frames=len(traj_g_x))
                              

        print('save_animation?')
        shuold_save_animation = int(input())

        if shuold_save_animation: 
            animation.save('basic_animation.gif', writer='imagemagick')

        plt.show()

    def _update_anim(self, i):
        # 全体
        self.dwa_imgs = []
        # DWApath用
        self.dwa_path_imgs = []
        # 障害物用
        self.obs_imgs = []

        self.traj_img.set_data(self.traj_x[:i+1], self.traj_y[:i+1])
        # 円を書く
        circle_x, circle_y, circle_line_x, circle_line_y = write_circle(self.traj_x[i], self.traj_y[i], self.traj_th[i], circle_size=0.2)

        self.robot_img.set_data(circle_x, circle_y)

        self.robot_angle_img.set_data(circle_line_x, circle_line_y)

        self.img_goal.set_data(self.traj_g_x[i], self.traj_g_y[i])

        self.traj_opt_img.set_data(self.traj_opt[i].x, self.traj_opt[i].y)

        count = 0
        # path_num = np.random.randint(0, len(self.traj_paths[i]), (1, 100))
        # print(path_num)
        
        for k in range(self.max_path_num):
            path_num = math.ceil(len(self.traj_paths[i])/(self.max_path_num)) * k
            
            if path_num >  len(self.traj_paths[i]) - 1:
                path_num = np.random.randint(0, len(self.traj_paths[i]))

            self.dwa_path_imgs.append(self.dwa_paths[k].set_graph_data(self.traj_paths[i][path_num].x, self.traj_paths[i][path_num].y))

        # obstacles
        for k in range(self.obstacles_num):
            self.obs_imgs.append(self.obs[k].set_graph_data(self.obstacles[k]))      

        self.step_text.set_text('step = {0}'.format(i))

        for img in [self.traj_img, self.robot_img, self.robot_angle_img, self.img_goal, self.step_text, self.dwa_path_imgs, self.obs_imgs, self.traj_opt_img]:
            self.dwa_imgs.append(img)

        return self.dwa_imgs
```

[0](https://qiita.com/MENDY/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FMENDY%2Fitems%2Fc96e112d6fa0d7e29029&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FMENDY%2Fitems%2Fc96e112d6fa0d7e29029&realm=qiita)

[39](https://qiita.com/MENDY/items/c96e112d6fa0d7e29029/likers)

いいねしたユーザー一覧へ移動

39