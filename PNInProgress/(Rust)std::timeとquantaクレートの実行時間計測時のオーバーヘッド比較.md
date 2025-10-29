# 比較する

## きっかけ

- <https://qiita.com/moriai/items/2e39357fc34c54f6de49>

## std::timeとquantaの違い

- (あくまで参考程度に)
- <https://docs.rs/quanta/latest/quanta/> より
- std::timeはOSに対してsystem call
- quantaはCPUのレジスタを直接読み取ってるみたいな感じだと思っている
  - TSC(Time Stamp Counter)というのを読んでるらしい
  - 古いCPUだとできないことがあるっぽくて、その場合はstd::timeを呼ぶらしい

## 比較

### 懸念と前提

- メモリの帯域幅とか関係ある？
- CPUの寿命が早まるとかあったらいやだなあ
- 多分電力とかで結果は多少揺れる

### 比較対象CPU

- intel Core Ultra 7 155H
  - 貸与ノートPC
- Intel Core i7-6700K
  - 貸与デスクトップ
- intel core i7 11700
  - 私物
- Apple M4 Pro
  - 私物

### 比較結果

- Clineちゃんに作ってもらったものを少し成型したプログラムで行いました(添付してあります)

```txt
M4 pro
Loop total:               221.667µs
std::Instant::now total:  12.740833ms
quanta::Clock::now total: 913.228µs
std - Loop (avg ns):      12.519 ns/call
quanta - Loop (avg ns):   0.692 ns/call

intel core i7 11700
Loop total:               217.393µs
std::Instant::now total:  12.221178ms
quanta::Clock::now total: 5.621124ms
std - Loop (avg ns):      12.004 ns/call
quanta - Loop (avg ns):   5.404 ns/call


```

## まとめ

- std::timeよりquantaの方がオーバーヘッドが少なそう
