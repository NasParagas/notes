---
title: "C++20 モジュールとCMakeLists.txtの書き方"
source: "https://zenn.dev/taku3/articles/50b7d9049eb502"
author:
  - "[[Zenn]]"
published: 2025-04-11
created: 2025-10-19
description:
tags:
  - "clippings"
---
5

1[tech](https://zenn.dev/tech-or-idea)

C++20で言語機能にモジュールが追加された。  
時は流れ、そろそろヘッダーをやめてモジュールを書きたい。

HelloWorld.ixx

```cpp
module; // これはグローバルモジュールフラグメント。
// ここにプリプロセスで処理するマクロ等を記述する。
#include <iostream>

export module HelloWorld; // エクスポートを書かなければ他のファイルから利用できない。
// ここからHelloWorldという名前のモジュールとなる。

export void HelloWorld() // 関数宣言もエクスポートを書かなければ他のファイルから利用できない。
{
    std::cout << "Hello, world!" << std::endl;
}
```

main.cpp

```cpp
// 呼び出し側は、こう書く。
import HelloWorld; // モジュールをインポートする構文。インクルードからの移行先。

int main()
{
    HelloWorld(); // インポートしたモジュールに含まれる関数を呼び出す。
    return 0;
}
```

うん。いい感じだ。  
クラスも書きたい。

Greeting.ixx

```cpp
module;
#include <iostream>
#include <string>

export module Greeting;

export class Greeting
{
public:
    Greeting(std::string sentence)
    :m_sentence{sentence}
    {}
    void Greet() // クラスにエクスポートを書けば、メソッドにエクスポートはいらないのだ。
    {
        std::cout << m_sentence << std::endl;
    }

private:
    std::string m_sentence;
};
```

main.cpp

```cpp
import HelloWorld;

int main()
{
    Greeting greeting("Hello, world!");
    greeting.Greet();

    // もちろんポインタでも呼び出せる
    Greeting *pGreeting = new Greeting("Hello, pointer!")
    pGreeting->Greet();
}
```

よし、その調子だ。  
構造体はどうだろう？  
クラスと違う点は、アクセス修飾子を書かなかったときのアクセシビリティだったな。  
そのアクセシビリティはパブリックだ。

Greetings.ixx

```cpp
module;
#include <string>

export module Greetings;

export struct Greetings
{
    std::string moning;
    std::string afternoon;
    std::string night;
};
```

main.cpp

```cpp
import Greeting;
import Greetings;

int main()
{
    Greetings greetings{ "Good moning!", "Good afternoon!", "Good night!"};

    Greeting moning(greetings.moning);
    moning.Greet();

    Greeting afternoon(greetings.afternoon);
    afternoon.Greet();

    Greeting night(greetings.night);
    night.Greet();
}
```

アクセシビリティしか違いがないから、書けて当然だったな。  
そういえば、さっきからグローバル名前空間に定義してばかりだ。  
もしもライブラリを作成して頒布するなら専用の名前空間に定義したい。

Greeting.ixx

```cpp
module;
#include <string>

export module Greeting;

namespace myLibrary // 名前空間にエクスポートは書かずとも他のファイルから利用できる。
{
    export class Greeting
    {
    public:
        Greeting(std::string sentence)
        :m_sentence{sentence}
        {}
        void Greet()
        {
            std::cout << m_sentence << std::endl;
        }

    private:
        std::string m_sentence;
    };
}

export module Greetings; // ひとつのファイルに複数のモジュールを含められる。
namespace myLibrary
{
    export struct Greetings
    {
        using namespace std;
        string moning;
        string afternoon;
        string night;
    };
}
```

main.cpp

```cpp
import Greetings;
import Greeting;

int main()
{
    // 名前空間へアクセスするにはスコープ解決演算子が必要である。
    myLibrary::Greetings greetings{"Hello", "Hello", "Hello"};
    // スコープ解決演算子の左側に何もないならグローバル名前空間の明示だ。
    ::myLibrary::Greeting greeting(greetings.moning);
    greeting.Greet();
}
```

よし！コーディングはあらかたできたから、ビルドしよう。  
CMakeを利用したい。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.28)
project(myModule)

add_library(moduleLib static) # モジュールの提供者はライブラリとしてビルドする。今回は静的。
target_compile_features(moduleLib PUBLIC cxx_std_20) # cxx_std_20は、バージョン3.12で追加された。
target_source(moduleLib PUBLIC
    FILE_SET CXX_MODULES # この"CXX_MODULES"が、かなめ。3.28で追加された。
    FILES
    Greeting.ixx)

add_execute(myexe) # これの引数にソースファイルを指定しても良いが、今回はそうしない。
# モジュールの利用者は、モジュールをリンクする。
target_link_libraries(myexe private
    moduleLib)
target_source(myexe private
    main.cpp)
```

これで、めでたしめでたし。ビルドできました。  
ん？　おや、メソッドの定義と実装のファイルを分けたいだって？

Greeting.ixx

```cpp
module;
#include <string>

export module Greeting;

namespace myLibrary // 名前空間にエクスポートは書かずとも他のファイルから利用できる。
{
    export class Greeting
    {
    public:
        Greeting(std::string);
        void Greet();

    private:
        std::string m_sentence;
    };
}
```

Greeting.cpp

```cpp
module;
#include <string> // メソッドの定義と宣言のファイルのどちらともに書かなければならない。

module Greeting; // 実装を書くファイルはエクスポートを書かない。

namespace myLibrary // 定義は、宣言と同じ名前空間でなければならない。
{
    Greeting::Greeting(std::string sentence)
    :m_sentence{sentence}
    {} 
}

void myLibrary::Greeting::Greet() // このようにスコープ解決演算子で名前空間をそろえても良い
{
    std::cout << m_sentence << std::endl;
}
```

MakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.28)
project(myModule)

add_library(moduleLib static)
target_compile_features(moduleLib PUBLIC cxx_std_20)
target_source(moduleLib PUBLIC
    FILE_SET CXX_MODULES
    FILES
    Greeting.ixx)

# 宣言のファイルとは別個に定義のファイルを記述する。"FILE_SET CXX_MODULES"は、いらない。
target_source(moduleLib PRIVATE
    Greeting.cpp)

add_execute(myexe)
target_link_libraries(myexe PRIVATE
    moduleLib)
target_source(myexe PRIVATE
    main.cpp)
```

ふむ。宣言と定義を分けて、モジュールをライブラリとしてビルドすれば再利用性が高そうだ。  
だがライブラリにしないなら、こう書ける。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.28)
project(myModule)

add_execute(myexe main.cpp) # ここに"main.cpp"を記述して目立たせている。ほかに意味はない。
target_compile_features(myexe PRIVATE cxx_std_20)
target_source(myexe
    FILE_SET CXX_MODULES
    FILES
    Greeting.ixx)
target_source(myexe PRIVATE
    Greeting.cpp)
```

解説は以上となります。ご閲覧ありがとうございました。

※拡張子について  
モジュールのファイルの拡張子は".ixx"または".cppm"が通例となっているようです。  
その２つであれば、Visual Studio 2022のソリューションエクスプローラーでアイコンが専用のものになります。  
ですが正しく `FILE_SET CXX_MODULES` を記述していれば、拡張子は何でもいいです。  
今回は、せっかくなので通例に従ってみました。

.ixxの"xx"の意味はC++の"++"を傾けたんでしょうかね？  
CMakeもそうしてるし。CXX\_で始まる変数多いもん。

追記：  
`.ixx` と`.cppm` は、コンパイラが拡張子によって動作を変えてモジュールインターフェース（モジュール宣言）のファイルだと認識します。モジュール定義のファイルの拡張子は `cpp` が良さそう。

5

1

5

1