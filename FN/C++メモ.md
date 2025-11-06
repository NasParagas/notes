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

- `const`をつけて定義した値は、そのスコープで値が変更されないことが約束される
- `constexpr`: コンパイル時に評価される
  - つまりコンパイル時には値が確定していることが要求される？
  - なんか実行時に確定でもいい場合はあるらしい？そもそもの理解が違うかも
  - C++14とかC++20以降あたりで制限が緩和されているらしい
- 基本的には、`constexpr`を使った方が性能(処理速度？)が向上するらしい
- 例

```cpp
const int ci = 17;
int i = 17;
constexpr double ced1 = 1.4 * ci;  // ok
constexpr double ced2 = 1.4 * i;  // コンパイルエラー
```

- `i`はコンパイル時には値が確定していない(後の処理で変わる可能性がある)ので、`ced2`がコンパイルエラーとなる
- `const`付きであれば初期化後には値が変更されないことが保証されているため、`ced1`もコンパイル時には値が確定していることが保証できる
- `const`オブジェクトに対して呼び出せるメンバ関数・演算子も`const`である必要がある
  - 例は`cpp_ground`の`2_4_1`を参照して
- `const`の位置に注意
  - `const int f(x)`とした場合には、返り値が`const`、つまり返り値を変更しないということが保証される
  - `int f(x) const`とした場合には、メンバ関数の処理がオブジェクトを変更しないことが保証される
  - 例(`cpp_ground`の`2_4_1`)

```cpp
class Vector
{
public:
    Vector(int num_elements);
    const double& operator[](int index) const;
    int size() const;
private:
///////
}
```

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

### 具象型

- 基本の考え方
  - `3.2.1`を読んどいて
- 例

```cpp
class complex
{
    double re, im;

public:
    // コンストラクタ
    complex(double r, double i): re{r}, im{i} {}
    complex(double r): re{r}, im{0} {}
    complex(): re{0}, im{0} {}  // デフォルトコンストラクタ

    // get
    double real() const { return re; }
    double imaginary() const { return im; }

    // set
    void real(double r) { re = r; }
    void imaginary(double i) { im = i; }

    // 算術定義(ユーザ定義演算子)
    complex& operator+=(complex z)
    {
        complex z2;
        re += z.re;
        im += z.im;
        return *this;  // このメンバ関数を実行したオブジェクトを参照する式(this自体はポインタ)
    }
};

// complexの内部データに直接アクセスする必要のない演算はクラス定義とは分離して記述できる
// すでに定義した演算を使って定義する
complex operator+(complex a, complex b) { return a+=b; }

int main()
{
    complex z;
    complex z2;
    z.real(10);
    z.imaginary(5);
    z2.real(4);
    z += z2;
    std::cout << z.real() << "\n" << z.imaginary() << std::endl;
}
```

- C++は、使わなくなったメモリを新しいオブジェクト用に割り当てられることを保証しない
  - つまり明示的な解放が必要。コンストラクタが割り当てたメモリはデストラクタによって解放する
- メモリ割り当て処理は裸で行わず、抽象化の設計に閉じ込めるようにする方が良い。そのためのコンストラクタとデストラクタ
  - 裸の`new`や`delete`はエラーやリークの元
- デストラクタの例(`3_2_1`):

```cpp
class Vector
{
  Vector( /* */);  // コンストラクタ
  ~Vector();  // デストラクタ
}

Vector::Vector(int num_elements)
       :element {new double[num_elements]}, num_elements{num_elements}
{
    for (int i = 0; i != num_elements; ++i)
    {
        element[i] = i;
    }
}

Vector::~Vector() { delete[] element; }
```

#### コンテナ

- 要素の集合を保持するオブジェクト
- 例: `Vector`など
- `コンストラクタ`で資源を獲得し、`デストラクタ`で開放する技法は、RAII(Resource Acquisition Is Initialization)と呼ばれる

### 抽象型

- 内部データを持たず、インターフェースのみを提供するクラス
  - 3.2.2を時間たってから改めて読んだらまた違う表現が思いつきそう
- 例

```cpp
class Container
{
public:
    virtual double& operator[](int) = 0;
    virtual int size() = 0;
    virtual ~Container() {};
};
```

- `Container`は抽象クラスと呼ぶ。初期化するようなデータを持たないためコンストラクタは必要ない
  - デストラクタが必要な理由はいまいちわかってない(データはないけど領域は確保されてるのかな？それかコンストラクタは実質実装の方がになっているみたいな雰囲気？)が、順番的には派生クラス→基底クラスの順で解体されるはず
    - 基底クラス自体のデストラクタが呼ばれたときにおかしくならないようにっぽい
- 実装の例
  - この場合、"`List_container`は`Container`を実装している"と表現するっぽい
  - メンバ関数やデストラクタについて、基底クラス`Container`のメンバをオーバーライドしていると表現する
  - ここでは記載していないが、派生クラスにはデータメンバや演算子を追加できる。柔軟性が上がるが、設計が難しいので使いどころ

```cpp
// 宣言
class List_container : public Container 
{
public:
    List_container() 
    { }
    List_container(std::initializer_list<double> il) : ld {il} 
    { }
    ~List_container()
    { }
    double& List_container::operator[](int index)
    { }
private:
    std::list<double> ld;
};

// 実装
List_container::List_container() 
{ }
List_container::List_container(std::initializer_list<double> il) : ld {il} 
{ }
List_container::~List_container()
{ }
double& List_container::operator[](int index)
{
    for (auto& x : ld)
    {
        if (index == 0)
        {
            return x;
        }
        --index;
    }
}
```

- 仮に、`Container`を利用する`use(&Container)`のような関数が存在する場合、基底クラスである`Container`の派生クラスでも使用することが出来る
  - >この柔軟性の代償は、オブジェクトの操作をポインタや参照経由で行わなければならないことだ


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


