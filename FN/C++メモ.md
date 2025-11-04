# memo

- 特記なければC++11を基にしている。いつか更新
- cpp_playgroundに対応するそれぞれcppファイルがある

## `/n`と`std::endl`の違い

### 参考

- <https://runebook.dev/ja/articles/cpp/io/manip/flush#google_vignette>  
- copilot

### 中身

- `/n`は改行文字、`std:endl`は`os.put(os.widen('\n')) << std::flush`と等価
- `std::flush`をするかしないか
- それはなに
  - 「出力ストリームへのバッファを強制的にフラッシュする」
- 実行時間自体は結構違かった
- 即時性が求められたりバッファリングされたら困る場合は`endl`、それ以外は`\n`？
  - ユーザーへのターミナル上での指示待ちや異常終了の可能性がある場所のエラーログとかリアルタイムログとか？
    - std::cerrはデフォルトで毎回flushするらしい

## 初期化の記法

- `=`の初期化以外に、`{}`で囲む初期化の方法もある
- `{}`を使った初期化は縮小変換でコンパイルエラーを出してくれる
  - `=`の方も`cmake --build`だと警告出してくれるのか

```cpp
int i1 = 7.1  // 7
int i2 {7.1}  // コンパイルエラー
```

- でも型を明示したい状況とかじゃない限りは`auto`でいい

## `const`と`constexpr`

- `const`をつけて定義した値は、そのスコープで変更されないことが約束される
- `constexpr`: コンパイル時に評価される
  - つまりコンパイル時には値が確定していることが要求される？
  - なんか実行時に確定でもいい場合はあるらしい？そもそもの理解が違うかも
  - C++14とかC++20以降あたりで制限が緩和されているらしい
- 基本的には、`constexpr`を使わないよりも使った方が性能(処理速度？)が向上するらしい
- 例

```cpp
const int ci = 17;
int i = 17;
constexpr double ced1 = 1.4 * ci;  // ok
constexpr double ced2 = 1.4 * i;  // コンパイルエラー
```

- `i`はコンパイル時には値が確定していない(後の処理で変わる可能性がある)
- `const`付きであれば、初期化後には値が変更されないことが保証されているため、`ced2`もコンパイル時には値が確定していることが保証できる

## `nullptr`

- ポインタの指す先は、オブジェクトとなるようにしなければならないという前提
- 指すべき場所を持たないとき(リストの終点など)や、有効なオブジェクトがない(ポインタ型の初期化時など)ときに`nullptr`を用いる
- 実引数のポインタが何らかのオブジェクトを指していることを期待するのであれば、それを確かめるようにする
  - `nullptr`になってないかどうかを確かめる

```cpp
// あとでかくます
int count_x
```

## ポインタ

```cpp
char v[6];

// ポインタ型の宣言
// 何も指示さない場合はnullptrで初期化j
char* p = nullptr;

// *(content of ~), &(address of ~) の使い方
char* p4 = &v[3];
char x = *p;
```

## `struct`

- 必要な構成要素を単一のデータ構造としてまとめる

```cpp
struct Vector 
{
    int num_elements;
    double *element;
};
```

- 例えば以下のように初期化する

```cpp
void vector_init(Vector &vector, int num_elements)
{
    vector.element = new double[num_elements];
    vector.num_elements = num_elements;
    for (int i; i < vector.num_elements; i++)
    {
        vector.element[i] = i;
    }
}
```

- また、メンバへのアクセス方法は以下の通り

```cpp
void f(Vector v, Vector& rv, Vector* pv)
{
    int i1 = v.num_elements;
    int i2 = rv.num_elements;
    int i3 = pv->num_elements;
}
```

## クラス

- インターフェースと実装の独立
  - ユーザーがアクセスできる部分とできない部分
  - ここではインターフェース: publicメンバ、実装: privateメンバ
- メンバの集まりを定義したもの
  - 関数、データ、型...
- 例

```cpp
class Vector 
{
 public:
    // コンストラクタ: クラスのオブジェクト初期化時に使われることが保証される
    // int型の引数を一つ要求
    // :以降は初期化の手順
    Vector(int num_element) :element{new double[num_element]}, num_element{num_element} {}

    const double& operator[](int s) 
    {
        return element[s];
    }
    int size() 
    {
        return num_element;
    }

 private:
    double* element;
    int num_element;
};


```

## 列挙体

```cpp
// 定義
enum class Color
{
    red,
    blue,
    green
};

// 初期化
Color color = Color::red;
```


