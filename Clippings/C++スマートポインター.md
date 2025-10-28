---
title: "C++スマートポインター"
source: "https://wfalps.hatenablog.com/entry/2021/01/16/231226"
author:
  - "[[wfalps]]"
published: 2021-01-16
created: 2025-08-21
description: "C++では、メモリを動的に確保・解放することができます。しかしそれが困難のもとでもあり、動的に確保されたメモリが正しく解放されないと、メモリリークが発生してしまいます。メモリリークが蓄積するとアプリケーションの動作が遅くなったり、パソコン全体の動作が遅くなったりしてしまうことがあります。なお、Visual Studioを利用している場合、デバッガを利用してメモリリークを検出する方法があります： CRT ライブラリを使用したメモリ リークの検出 - Visual Studio | Microsoft Docs #define _CRTDBG_MAP_ALLOC #include <stdlib.…"
tags:
  - "clippings"
---
[C++](http://d.hatena.ne.jp/keyword/C%2B%2B) では、メモリを動的に確保・解放することができます。しかしそれが困難のもとでもあり、動的に確保されたメモリが正しく解放されないと、 [メモリリーク](http://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が発生してしまいます。 [メモリリーク](http://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が蓄積するとアプリケーションの動作が遅くなったり、パソコン全体の動作が遅くなったりしてしまうことがあります。

なお、 [Visual Studio](http://d.hatena.ne.jp/keyword/Visual%20Studio) を利用している場合、デバッガを利用して [メモリリーク](http://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) を検出する方法があります： [CRT ライブラリを使用したメモリ リークの検出 - Visual Studio | Microsoft Docs](https://docs.microsoft.com/ja-jp/visualstudio/debugger/finding-memory-leaks-using-the-crt-library?view=vs-2019)  

```cpp
#define _CRTDBG_MAP_ALLOC
#include <stdlib.h>
#include <crtdbg.h>

int main()
{
    auto leak = new int[3];
    // delete[] leak;

    _CrtDumpMemoryLeaks();
}
```

デバッガの出力：

```cpp
Detected memory leaks!
Dumping objects ->
{76} normal block at 0x00A14F98, 12 bytes long.
 Data: <            > CD CD CD CD CD CD CD CD CD CD CD CD 
Object dump complete.
```

### スマートポインター

[C++](http://d.hatena.ne.jp/keyword/C%2B%2B) 11にて、スマート [ポインター](http://d.hatena.ne.jp/keyword/%A5%DD%A5%A4%A5%F3%A5%BF%A1%BC) であるstd::unique\_ptr, std::shared\_ptr, std::weak\_ptrが追加されました。スマート [ポインター](http://d.hatena.ne.jp/keyword/%A5%DD%A5%A4%A5%F3%A5%BF%A1%BC) とは、動的に確保されたメモリを適切に管理してくれるような仕組みです。

#### std::unique\_ptr

あるメモリに対して、1つのポインタのみが所有権を保持しているようなポインタです。ポインタが使用されなくなると、自動的にメモリが解放されます。生のポインタとほぼ同様のメモリ効率と処理速度を持ちます。

```cpp
{
    // c++14以降であればstd::make_uniqueにてポインタの作成が可能。
    auto p1 = std::make_unique<int>(); 

    // コピーすることはできない。
    // auto p2 = p1; // コンパイルエラー。

    // moveセマンティクスによって所有権を移行させることができる。
    std::unique_ptr<int> p3 = std::move(p1);
} // scopeを出たところでメモリが解放される。
```

メモリ解放の際に使用されるdeleterを指定することができ、deleterによっては生ポインタと比べてメモリ効率などが落ちてしまいます。

#### std::shared\_ptr

あるメモリに対して、複数のポインタが所有権をシェアしているようなポインタです。所有権を保持しているポインタがなくなったタイミングでメモリが解放されます。あるオブジェクトを複数のコードから参照したい場合に便利です。「コン [トロール](http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB) ブロック」と呼ばれる参照カウンタなどのデータを保持しているため、メモリ使用量などは生のポインタに比べて増えます。

```cpp
void func(std::shared_ptr<int> ptr)
{
    std::cout << ptr.use_count() << std::endl;
}

int main()
{
    auto p1 = std::make_shared<int>(3);

    // 参照カウンタ1
    std::cout << p1.use_count() << std::endl;

    {
        auto p2 = p1;
        // 参照カウンタ2 (p1とp2)
        std::cout << p1.use_count() << std::endl; 
    }

    // 参照カウンタ1 (p2が解放されp1のみ)
    std::cout << p1.use_count() << std::endl; 

    // 関数内で参照カウンタ2 (引数としてコピーされ参照カウンタが増える)
    func(p1);  

    // 参照カウンタ1 (p1のみ)
    std::cout << p1.use_count() << std::endl; 
}
```

生のポインタを取り出すときにはget()を使います。この場合p1の所有権に変化はありません。

```cpp
auto rawp = p1.get();
std::cout << *rawp << std::endl;
```

unique\_ptrは簡単にshared\_ptrに変換することができます。メモリ効率の良いunique\_ptrで定義しておき、所有権のシェアが必要になったタイミングでshared\_ptrに変換する、または関数の戻り値をunique\_ptrにしておき、受取先で必要に応じてshared\_ptrに変換するようなこともできます。

```cpp
auto up = std::make_unique<int>(5);
std::shared_ptr<int> sp = std::move(up);
```

unique\_ptrと同様メモリ解放の際に使用されるdeleterを指定することができます。

#### std::weak\_ptr

shared\_ptrによって参照されているあるメモリを、所有権を持つことなく参照することのできるポインタです。対象となるメモリが解放されるのをブロックしたくはないけれども、まだ解放されていないのであれば参照したい、といった使い方ができます。循環参照などでメモリが解放されずにリークしてしまう問題を防ぐことができます。shared\_ptrと同様「コン [トロール](http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB) ブロック」と呼ばれるデータを保持します。

参照する際には、lock()を呼び出すことによってshared\_ptrとして取り出します。もし既に対象メモリが解放されていたら、lock()はnullポインタを返します。

```cpp
auto sp = std::make_shared<int>(3);

std::weak_ptr<int> wp(sp);
// 参照カウンタ1 (spのみ, wpは参照カウンタをアップデートしない。)
std::cout << sp.use_count() << std::endl; 

// sp.reset();

if (std::shared_ptr<int> p2 = wp.lock())
{
    // 参照先が利用可能な時のみ処理を行う。
    // 参照カウンタ2 (p1とp2)
    std::cout << sp.use_count() << std::endl; 
    std::cout << "p2 = " << *p2 << std::endl;
}

// 参照カウンタ1 (p1のみ)
std::cout << sp.use_count() << std::endl;
```

### イメージ

![C++ smart pointers](https://cdn-ak.f.st-hatena.com/images/fotolife/w/wfalps/20210105/20210105234458.png)

C++ smart pointers

### Links

[C++11スマートポインタ入門 - Qiita](https://qiita.com/hmito/items/db3b14917120b285112f)  
[4\. Smart Pointers - Effective Modern C++ \[Book\]](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/ch04.html)  
[youtube back to basics smart pointer - Bing video](https://www.bing.com/videos/search?view=detail&mid=8BDCC302AF5DEB6818F98BDCC302AF5DEB6818F9&q=youtube+back+to+basics+smart+pointer&shtp=GetUrl&shid=a82f7f7d-78eb-4e4d-9933-f27def995e4c&shtk=QmFjayB0byBCYXNpY3M6IFNtYXJ0IFBvaW50ZXJzIC0gUmFpbmVyIEdyaW1tIC0gQ3BwQ29uIDIwMjA%3D&shdk=aHR0cHM6Ly9jcHBjb24ub3JnLyBodHRwczovL2dpdGh1Yi5jb20vQ3BwQ29uL0NwcENvbjIwMjAvYmxvYi9tYWluL1ByZXNlbnRhdGlvbnMvYmFja190b19iYXNpY3Nfc21hcnRfcG9pbnRlcnMvYmFja190b19iYXNpY3Nfc21hcnRfcG9pbnRlcnNfX3JhaW5lcl9ncmltbV9fY3BwY29uXzIwMjAucGRmIC0tLSBGcm9tIHRoZSBsaWJyYXJ5J3MgcGVyc3BlY3RpdmUsIGFuIGVzc2VudGlhbCBmZWF0dXJlIGluIEMrKzExIHdhcyBzbWFydCBwb2ludGVycy4gU2luY2UgQysrMTEsIHdlIGhhdmUgZm91ciBkaWZmZXJlbnQgc21hcnQgcG9pbnRlcjogc3RkIC4uLg%3D%3D&shhk=Et9PNxR%2FrPOMXEwc4p3w4DBXADFMd4RjTAjhYJ1hx48%3D&form=VDSHOT&shth=OSH.YJMVekU7JtXOWpl%252FyxSd3g)