# Rustのメモ帳

### dockerでの環境構築

```dockerfile
FROM ubuntu:24.04
RUN apt update && apt install -y curl build-essential
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | \
    sh -s -- -y --default-toolchain stable
ENV PATH="/root/.cargo/bin:${PATH}"
CMD ["/bin/bash"]
```

### rustcを使ったbuild、実行

```rust
// ~/Projects/hello_world/main.rs
fn main() {
    println!("Hello, world!");
}
```

```bash
# ~/Projects/hello_world
rustc main.rs
main
# >> Hello, World!
```

### cargoを使ったpackageの作成、buildと実行

```bash
# ~/Projects
cargo new hello_cargo
cd hello_cargo
```

- `Cargo.toml`にはプロジェクトの名前、バージョン、使用するRustのエディションが記載される
  - `name`の部分が実行ファイルの名前にもなる
  - dependencies以下にはプロジェクトが依存するクレート(パッケージ)を追記していく

```bash
cargo build
```

- 実行ファイルは`./target/debug/hello_cargo`
- 生成される`Cargo.lock`にはプロジェクト内の依存関係のバージョン情報が記録されている
- `cargo run`でビルド＆実行
- `cargo check`でコンパイル可能か確認できる
- `cargo build --release`で最適化された状態の実行ファイルをコンパイルできる。この時の実行ファイルは`./target/release/hello_cargo`
  - 実行ファイルの実行速度が上がるが、コンパイルに時間がかかる
- `cargo clean`でビルドしたファイルを消せる
- `cargo help <コマンド名>`でコマンドの詳細を確認できる

### cargo.tomlにcrateを追加

- [dependencies]以下に`rand = "0.8.3"`などと記入する
  - 実際には`"^0.8.3"`の省略だが、互換性のためにより上位のバージョンがある場合でも`0.8.3~0.9.0未満`のバージョンが選ばれる
- これを記載して`cargo build`を行ったとき、build時に必要なライブラリがコンパイルされ、`cargo.lock`にクレートのライブラリなどの正確なバージョン関係が追記される
  - `0.9.0`と記載してから再度buildを行うと、その新しいバージョンに従って必要なライブラリのバージョンなども自動で合わせてくれる
  - コードの中では`use rand:Rng`のように使う
    - `rand`: クレート
    - `Rng`: トレイト
    - `thread_rng()`: 関数
    - `gen_range()`: メソッド
    - `cargo run`でクレートの説明の`html`が生成される
      - 本来は`--open`でそのまま開いてくれるが、wslはダメみたい

## 型

### ユニット型

[参考](https://doc.rust-lang.org/std/primitive.unit.html)

- 空のタプル(スカラー型)
- 返り値が何もないことを表す
- 「ほかに意味のある値を返せないときに使う」
  - 例えばmain関数はユニット型のユニットを返す？
  - 値であり型でもある
  - `nullptr`で参照解決できちゃうのはおかしいでしょうという前提
- 例えば以下のように返り値として使うこともできる(もちろん省略できる)

```rust
fn hello() -> () {
    println!("hello");
}

fn main() -> () {
    hello();
}
```

- こんなこともできる(ユニット型に代入できるのはユニットのみ)

```rust
    let mut a: Vec<()> = vec![];
    a.push(());

    let b :() = ();
```

### 配列

- 初期化

```rust
let array1 = [1,2,3,4,5];
let array2 = ["monnnitiha"; 10]
```

- 長さはコンパイル時に決定される
- 初期化されていない配列は宣言できない

### ベクタ

- 使用例

```rust
    // ベクタ
    // 初期化
    let mut primes = vec![2,3,5,7];
    let mut vec1 = vec![0; 3];
    let mut pal = Vec::new();

    // collectメソッドを使用した初期化
    let languages: Vec<String> = std::env::args().skip(1).collect();
    for l in languages
    {
        println!("{}: {}",
            l, 
            if l.len() % 2 == 0 { "functional" }
            else { "imperative" }
        );
    }


    // メソッド使用例
    // push
    primes.push(11);
    primes.push(13);
    assert_eq!(primes.iter().product::<i32>(), 30030);

    assert_eq!(vec1, [0,0,0]);

    // pop
    pal.push("step");
    pal.push("on");
    assert_eq!(pal, ["step","on"]);
    pal.pop();
    assert_eq!(pal, ["step"]);

    // capasity
    let mut v = Vec::with_capacity(1);
    assert_eq!(v.capacity(), 1);
    v.push(1);
    v.push(2);
    assert_eq!(v.len(), 2);
    println!("{}", v.capacity());  // 4 (2 -> 4 -> 8 -> 16...)

    // insert
    use quanta;
    vec1.insert(1, 1);
    assert_eq!(vec1, [0,1,0,0]);
    let clock = quanta::Clock::new();
    let mut vec2 = vec![0;1_000_000_000];
    let start = quanta::Instant::now();
    vec2.insert(0, 0);
    let stop = quanta::Instant::now();
    println!("{:?}", stop.duration_since(start));  // 0.25s程度
    let start = quanta::Instant::now();
    vec2.insert(900_000_000, 0);
    let stop = quanta::Instant::now();
    println!("{:?}", stop.duration_since(start));  // 0.015s程度
```

- 動的にサイズ変更が可能(ヒープ上に確保される)
- 以下の三つの値で構成される
  - ヒープ上に確保されるバッファ
    - バッファに達しているときに新たな要素を追加しようとすると、より大きなバッファが確保され、要素がコピーされ、ベクタのポインタと容量が更新されて新しいバッファを刺すようになり、古いバッファが解放される
  - バッファに保持できる容量
  - 現在保持している要素数
  - vec!とVec::new()は何が違う？
    - Vec::with_capacity()というものがあり、vec!はそれを使っている
      - あらかじめ必要な要素数がわかっているとき、バッファの再確保をしないようにするために用いる
      - コンパイル時に要素数を判断してる？

### スライス

- スライスは任意の長さでありうるので、直接変数に格納したり関数の引数として渡すことはできない（つまりつねに参照として渡される）
  - ？
  - 大きさが決まらないから静的に(コンパイル時に)スライスとしては決定できないってこと？
- スライスの参照はファットポインタ(スライスの最初の要素を示すポインタとスライスに含まれる要素数)

```rust
    // 宣言方法(明示的にすることはあまりないと思うけど)
    let v = vec![0.1, 0.2, 0.3];
    let a =     [0.1, 0.2, 0.3];
    let sv: &[f64] = &v;
    let sa: &[f64] = &a;
```

- 上の例では、ベクタ、配列ともにスライスの参照へと変換している
- つまり、ベクタや配列で使いたいメソッドにおいて、スライスへの参照を引数として取るようにすれば、どちらでも使用可能になる

```rust
    fn print_elem(n: &[f64]) -> ()
    {
        for elem in n 
        {
            println!("{}", elem);
        }
    }
    // どっちでも使える
    print_elem(sv);
    print_elem(sa);
    print_elem(&sv[0..1]);
    print_elem(&sa[1..]);
```

- 実際に、`len()`や`sort()`などもスライス型[T]の参照に対するメソッドとして定義されている

![alt text](<../_ATTACHMENT/IMG_7596 2.jpg>)

### Result型

- エラー処理に使える！！！

### その他

- 列挙型=enum
  - とりうる値として決まった数の列挙子(variant)を持つ
- `except`メソッドはその
