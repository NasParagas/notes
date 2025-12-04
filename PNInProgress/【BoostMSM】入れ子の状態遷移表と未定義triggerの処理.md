## boostmsmとは
C++のライブラリBoostで提供されている状態管理のためのパッケージ(MSM＝meta state machine ）

ここが公式のドキュメントだと思っている

この記事に辿り着いたということは表題のどちらかが目的だと思うので、早速説明
入れ子でない場合など、シンプルな場合のやり方はhttps://boostjp.github.io/tips/finite_state_machine.html
を参考にすればできると思う

## 入れ子の状態遷移表定義
入れ子の状態遷移表とはこういう感じのやつ↓

## 未定義triggerの処理
以上のように定義した状態遷移表では、以下が定義されているtriggerとなる

その状態において定義されていないtriggerが呼ばれた際、state machine側は例外を投げるのではなくno_transition関数を実行する
