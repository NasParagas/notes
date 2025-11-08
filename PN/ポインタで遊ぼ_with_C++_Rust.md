# 遊ぶよ

## 前提

- C++およびRustの話です

## コピーとムーブ

- こんな自前のvectorがあったとする

```cpp
class Vector
{
public:
    // コンストラクタ
    Vector(int num_elements)
    ~Vector();
private:
    double* element;  // 配列要素へのポインタ
    int num_elements
}
s
// 実装
Vector::Vector(int num_elements)
       :element {new double[num_elements]}, num_elements{num_elements}
{
    for (int i = 0; i != num_elements; ++i)
    {
        element[i] = i;
    }
}
```

- この例のようにコピーコンストラクタを定義せずに初期化や代入を行う場合、デフォルトではshallow copy、つまりポインタのコピーが行われる

```cpp
Vector v1(3);
Vector v2 = v1;
Vector v3 {v1};

// ヒープ領域にある方たち
std::cout << &v1[0] << "\n";
std::cout << &v2[0] << "\n";
std::cout << &v3[0] << "\n";
std::cout << &v1[1] << "\n";
std::cout << &v2[1] << "\n";
std::cout << &v3[1] << "\n";

// 0x5624c7bd32b0
// 0x5624c7bd32b0
// 0x5624c7bd32b0
// 0x5624c7bd32b8
// 0x5624c7bd32b8
// 0x5624c7bd32b8
// free(): double free detected in tcache 2
// Aborted (core dumped)
```

