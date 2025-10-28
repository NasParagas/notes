
前置き
- 備忘録の側面が強いので、体系的にはまとまっていません。
- ROS2のスタディを行っていく中で追記したくなったこと、理解の内容が変わったことがあれば後々修正・追記します
- スタディに用いたのは[ここ](https://cpp-lang.sevendays-study.com/index.html)と「独習C++」です。そのほか参考にしたサイトなどは各項目に載せています。

# 書いてあること
- C#にはない(と思われる)概念
  - ヘッダーファイル、include、ストリームなど
- C#基礎講座で詳細には扱われなかった概念
  - 継承、ポリモーフィズムなど
- C++ && ROS2を使ううえで覚えておきたい概念（適宜追記）

具体的に何が書いてあるか
  - include
  - ストリーム
  - ポインタ
  - スタックとヒープ(メモリ)
  - クラス、アクセス修飾子、new・delete、コンストラクタ、デストラクタ、継承、多態性
    - 概念自体は同じなのでC++ではどう描くのかという観点からのみ
 - 値渡し、ポインタ渡し、参照渡し(予定)
- スマートポインタ(予定)
 - テンプレート


# 書かれていないこと
- C#と記法・使用感ともに大きくは違わなそうと判断したもの
  - データ型、キャスト、制御構文などなど..
- **C++の文法の網羅的な説明**


# 環境
- Ubuntu22.04 LTS (Virtual Box)
- VSCode
- C++ 14

前置き終了

---

# 以下内容

## **include**
使いたい機能が含まれたライブラリのヘッダーをincludeする。例えば標準入出力を扱いたくて`iostream`ライブラリを使いたい場合は
```C++
#include <iostream>
```
とファイルの文頭に記入する
自作のヘッダーをincludeする場合は、後述する`.h`ファイルと`cpp`ファイルを作成し
```C++
#include "my_class.h"
```
と文頭に記入


## ストリーム
```C++
#include <iostream>

std::string str1;
std::cout << "文字列を入力: ";
std::cin >> str1;
std::cout << "入力された文字列は"+「str1」 << "です" << std::endl;
std::cout << std::hex << 255 << std::endl; // ff
```
- `iostream`: 入出力機能クラス
- イメージ的には「`cout`には出力したいオブジェクトを挿入(`<<`は挿入演算子)し、`cin`は入力したオブジェクトを変数に挿入する」みたいな演算の流れ。
  一番下は`255`を`hex`(16進数変換)に通してから出力しているみたいな
  - `std::endl`については[ここ](https://yutateno.hatenablog.jp/entry/2020/03/23/190059)にいろいろ面白いこと書いてある


## ポインタ
参考：
- https://c-lang.sevendays-study.com/ex-day3.html
- https://qiita.com/yokoto/items/5672ff20b63815728d90
- https://qiita.com/EqualL2/items/333c58343c22e99b5660

まず前提として
- 変数の値は(物理的な)メモリに書き込まれている
- アドレスは、その値が書き込まれているメモリ上の場所を示す
- **変数からは、アドレスと値を取得できる**

ことを覚えておく

### 変数のアドレスの取得
- `&`を使って変数が書き込まれているアドレス(メモリ上の位置)を取得
```c++
int num = 223;
std::cout << num << std::endl; //223
std::cout << &num << std::endl; //0x7ffd53b535c4 (人によって値は異なるはず)
```
### ポインタ変数(ポインタ)
- アドレスを保持するための変数
- `*`は関節参照演算子というらしい
例：
```C++
  int num = 223;
  int *num_p = nullptr;
  num_p = &num;
  std::cout << num << std::endl; //223
  std::cout << &num << std::endl; //0x7ffd53b535c4
  std::cout << num_p << std::endl; //0x7ffd53b535c4
  std::cout << *num_p << std::endl; //223

  *num_p = 100; //num_pのアドレスが示す先(0x7ffd53b535c4)に100を代入
  std::cout << num << std::endl; //numのアドレスも0x7ffd53b535c4なので、numも100になる
  std::cout << &num << std::endl; //0x7ffd53b535c4
  std::cout << num_p << std::endl; //0x7ffd53b535c4
  std::cout << *num_p << std::endl; //100
```

### (一次元)配列とポインタ
前提
- 配列は上記までと少し勝手が違う
- (多次元配列はまた少し違うらしいのでここでは考慮していない)

覚えておきたいこと
- 配列はそれ自体が先頭要素のアドレスを持っている

例：
```C++
#include <iostream>

int main() {
  int nums[5];
  char chars[5];
  int *p1 = nullptr; //nullポインタ
  char *p2 = nullptr;

  for (int i = 0; i < 5; i++) {
    nums[i] = i;
    chars[i] = 'A' + i;
  }

  p1 = &nums[0];
  p2 = &chars[0];
}
```


| 配列   | 各アドレス | 該当するポインタ | ポインタ変数の値 |  値  |
| ------ | --------- | -------- | -------- | ----- |
| nums[0] | &nums[0]   | p1       | *p1      | 0     |
| nums[1] | &nums[1]   | p1+1     | *(p1+1)  | 1     |
| nums[2] | &nums[2]   | p1+2     | *(p1+2)  | 2     |
| nums[3] | &nums[3]   | p1+3     | *(p1+3)  | 3     |
| nums[4] | &nums[4]   | p1+4     | *(p1+4)  | 4     |

- また、この例でいうと`nums`、`chars`は`int*`型、`char*`型に暗黙変換されうることを覚えておく。例として
```
nums == &nums[0];
chars == &chars[0];
```
はtrueとなる

### その他配列関連で頭に入れておいた方がいいかもしれないこと
- Char型の配列の各要素にはそれぞれ文字列のコードが入っていて、配列の最後には`∖0`(NULL文字)が入る。
つまりchar型の配列の途中に`∖0`があった場合は、どんなに長いchar型配列であってもそこで文字列は終了する。

## メモリ管理
参考：
https://zenn.dev/rita0222/articles/e6ff75245d79b5
- これの(1)~(3)がとても参考になる

- スタック
  - 関数内で定義した変数が配置される領域。スタックという名の通り、最初に定義された変数が領域の末尾側(アドレスの大きい側)に配置される
  - 変数用の領域確保は、その時点での配置されている一番上のアドレスから、確保したい型のサイズ分だけアドレスの小さい方にスライドするだけで良いので、**処理が高速**
  - ただし、コンパイル時に型のサイズが確定しないものは変数として定義できない(`std::string`とか)し、スタックに積んだ変数の大きさを後から変えることは出来ない(`int`とか)

- ヒープ
  - 好きな時に明示的に確保して、不要になったら解放する領域
  - 領域の確保、解放をしすぎると処理に時間がかかったり、メモリ領域が飛び飛びになるフラグメンテーションが起こったりするらしい

- 上記は**使い分けが大事**だ(と思う)が、まだ感覚がつかみ切れていない
- また、`std::vector`などメモリを管理するのに便利なものも多数あるので、何かするときには便利そうなのがないか調査すること


## クラス、アクセス修飾子、new・delete、コンストラクタ、デストラクタ、継承、多態性
- 各概念の内容自体はC#と同様(だと思ってる)。宣言の仕方や記法が異なる
  - なのでここでは「どう書けばいいか」のみを例示するだけ。各項目の説明はしていない
- クラス・クラス内メソッドの宣言は`.h`で、処理の実装はヘッダーファイルをincludeした`.cpp`の中で行い、メソッドを使用するmainは別の`.cpp`に記述する
- そのほか、各項目の宣言場所は下例にコメントで記載してあるので適宜参照

  <summary>例：</summary>

  car.h
  ```C++
  #ifndef _CAR_H_  // ifndef, define, endifは後述
  #define _CAR_H_
  class Car {
  public:
    Car();  // コンストラクタ
    Car(int fuel, int migration); // オーバーロード
    virtual ~Car();  // デストラクタ(virtualで宣言)
    void move();
    void supply(int fuel);

  private:
    int fuel_;
    int migration_;
  };

  #endif  // _CAR_H_
```

  car.cpp
  ```cpp
  #include "car.h" // car.hで宣言した奴らについての処理を記述するよ~的な

  #include <iostream>

  Car::Car() : fuel_(0), migration_(0) {
    std::cout << "Carオブジェクト生成" << std::endl;
  } // コンストラクタの処理定義(fuel_(0)はfuel_を0で初期化してる)

  Car::Car(int fuel, int migration) : fuel_(fuel), migration_(migration) {
    std::cout << "Carオブジェクト生成2" << std::endl;
  } // オーバーロード

  Car::~Car() {
    std::cout << "Carオブジェクト破棄" << std::endl;
  }

  void Car::move() {
    if (fuel_ > 0) {
      migration_++;
      fuel_--;
    }
    std::cout << "移動距離: " << migration_ << "  残り燃料: " << fuel_ << std::endl;
  }

  void Car::supply(int added_fuel) {
    fuel_ += added_fuel;
    std::cout << "残り燃料: " << fuel_ << std::endl;
  }
  ```

  ambulance.h
  ```cpp
  #ifndef _AMBULANCE_H_
  #define _AMBULANCE_H_

  #include "car.h"  // 継承

  class Ambulance : public Car {
  public:
    Ambulance();
    virtual ~Ambulance();
    void savePeople();

  private:
    int phone_num_;
    int fuel_;
    int migration_;
  };

  #endif  // _AMBULANCE_H_
  ```

  ambulance.cpp
  ```c++
  #include "ambulance.h"

  #include <iostream>

  Ambulance::Ambulance() : phone_num_(119) {
    std::cout << "Ambulanceオブジェクト生成" << std::endl;
  }

  Ambulance::~Ambulance() {
    std::cout << "Ambulanceオブジェクト破棄" << std::endl;
  }

  void Ambulance::savePeople() {
    std::cout << "救急救命活動_呼び出しは" << phone_num_ << "番" << std::endl;
  }

  ```

  main.cpp
  ```cpp
  #include "car.h"
  #include "ambulance.h"

  int main() {
      Car c1; 
      c1.supply(10);
      c1.move();
      c1.move();

      Car *c2; // Carのポインタ変数
      c2 = new Car(10, 0); // オブジェクト生成、メモリ割り当て
      c2 -> supply(5);
      c2 -> move();
      c2 -> move();
      delete c2; // メモリ解放
      c2 = nullptr;

      Ambulance ambulance;
      ambulance.supply(10);
      ambulance.move();
      ambulance.savePeople();

      return 0;
  }
  ```

  出力
  ```
  Carオブジェクト生成
  残り燃料: 10
  移動距離: 1  残り燃料: 9
  移動距離: 2  残り燃料: 8
  Carオブジェクト生成2
  残り燃料: 15
  移動距離: 1  残り燃料: 14
  移動距離: 2  残り燃料: 13
  Carオブジェクト破棄 //2
  Carオブジェクト生成
  Ambulanceオブジェクト生成
  残り燃料: 10
  移動距離: 1  残り燃料: 9
  救急救命活動_呼び出しは119番
  Ambulanceオブジェクト破棄
  Carオブジェクト破棄
  Carオブジェクト破棄
  ```
  

- ifndif, define, endif
	同じヘッダーファイルが異なるcppファイルなどから複数回includeされた結果、重複定義によってエラーが返されるのを防ぐためのもの
	参考:https://programgenjin.hatenablog.com/entry/2019/02/22/210141
<br>

## virtual(仮想関数)について（**理解浅め**）
  - 参考：
  https://cpp-lang.sevendays-study.com/ex-day6.html
  https://qiita.com/ketaro-m/items/80ae54a8286c1bb610d7
  https://programming.pc-note.net/cpp/virtual.html

  - どういうモチベーション?
  **基底クラスから派生クラスのメンバ関数を呼び出したい**
  
  `virtual`は**実行時に動的に実体の型を調べて関数を選択する**
  ```c++
  Parent *c1 = new Children();
  Children *c2 = new Children();
  c1->func(); // "Parent"
  c2->func(); // "Children"
  c1->v_func(); // "Children"
  c2->v_func(); // "Children"
  ```
  - `virtual`をつけないとこれができない。これは**デストラクタ**も同様で、デストラクタを仮想化していなかった場合、`c1`を`delete`したときに基底クラスから派生クラスのデストラクタが呼べないため、部分的にしかオブジェクトが破棄できない場合がある。基底クラスのデストラクタに`virtual`をつけるべきのはこれが理由


## スマートポインタ
- 動的なメモリの確保(new, delete)を勝手にやってくれる機能
- [C++20スマートポインタ入門 #C++ - Qiita](https://qiita.com/hmito/items/9b35a2438a8b8ee4b5af)
	- どの機能がどのバージョンで使えるか注意




## (関数)テンプレート
- 定義時には引数や戻り値の型を明確には決めず、コンパイル時に各々の処理で与えられた型をつかえるようにしますよみたいな感じ。下の例を見た方がわかりやすいよ↓


<summary> 例 (同ファイルにmainも書いてるのは許して) </summary>
- テンプレートを使ってないver（intとfloatのoverload）

```C++
#include <iostream>

int multi_add(int a, int b, int c) {
  int r = a * b + c;
  return r;
}

float multi_add(float a, float b, float c) {
  float r = a * b + c;
  return r;
}

int main() {
  std::cout << multi_add(1, 2, 3) << std::endl;
  std::cout << multi_add(1.01233f, 2.31233f, 4.91333f) << std::endl;
  std::cout << multi_add(1.34, 4.234, 1.34) << std::endl;  // double型は定義していないのでコンパイルエラー
  std::cout << multi_add(1.01233, 1, 4.91333) << std::endl; // intへのimplicit castになる
  return 0;
}
```

```bash
#出力
5
7.25417
5
```

- テンプレートを使ってるver

```C++
#include <iostream>

template <typename T> T multi_add(T a, T b, T c) {
  T r = a * b + c;
  return r;
}

int main() {
  std::cout << multi_add(1, 2, 3) << std::endl;
  std::cout << multi_add(1.01233f, 2.31233f, 4.91333f) << std::endl;
  std::cout << multi_add(1.34, 4.234, 1.34) << std::endl;
  std::cout << multi_add(1.01233, 1, 4.91333) << std::endl; // castはされずコンパイルエラー
  return 0;
}
```

```bash
#出力
5
7.25417
7.01356
```


## その他メモ
- `const`修飾子をつけたら代入もできなくなる
- `this`はメンバ変数やメンバ関数にアクセスするためのポインタ
- |%ld|long|倍精度整数を10進で出力する|
- 実体のメンバ関数には`.`で、ポインタ経由でメンバ関数を使うときは`->`を使う
- 