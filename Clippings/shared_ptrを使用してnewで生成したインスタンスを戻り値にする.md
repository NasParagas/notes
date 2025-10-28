---
title: "shared_ptrを使用してnewで生成したインスタンスを戻り値にする"
source: "https://faithandbrave.hateblo.jp/entry/20080123/1201078947"
author:
  - "[[Akira Takahashi (id:faith_and_brave)]]"
published: 2008-01-23
created: 2025-08-21
description: "ユーザーに提供する関数では基本的に、newで生成したインスタンスを戻り値にしてはいけない (ユーザーが必ずdeleteしてくれることを期待してはいけない) #include <iostream> using namespace std; struct base { virtual base() {} virtual void disp() const { cout << \"base\" << endl; } }; struct derived : public base { virtual derived() {} virtual void disp() const { cout << \"der…"
tags:
  - "clippings"
---
ユーザーに提供する関数では基本的に、newで生成した [インスタンス](http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9) を戻り値にしてはいけない  
(ユーザーが必ずdeleteしてくれることを期待してはいけない)

```
#include <iostream>

using namespace std;

struct base {
    virtual base() {}
    virtual void disp() const { cout << "base" << endl; }
};

struct derived : public base {
    virtual derived() {}
    virtual void disp() const { cout << "derived" << endl; }
};

base* factory()
{
    return new derived;
}

int main()
{
    base* p = factory();
    p->disp();

    delete p; // 解放忘れるかも

    return 0;
}
```

だが、shared\_ptrのようなスマートポインタを使用すれば話は別だ

```
#include <iostream>
#include <memory>

using namespace std;
using namespace std::tr1; // C++0xではこの行は不要(std::shared_ptrになる予定なので)

struct base { ... };
struct derived : public base { ... };

shared_ptr<base> factory()
{
    return shared_ptr<base>(new derived);
}

int main()
{
    shared_ptr<base> p = factory();
    p->disp();

    return 0; // 使い終わったら必ずdelete
}
```

  
shared\_ptrを使用すれば、生成系の [デザインパターン](http://d.hatena.ne.jp/keyword/%A5%C7%A5%B6%A5%A4%A5%F3%A5%D1%A5%BF%A1%BC%A5%F3) がわりと簡単(安全)に実装できる

  

[« カリー化？](https://faithandbrave.hateblo.jp/entry/20080123/1201079590) [random\_number\_generator »](https://faithandbrave.hateblo.jp/entry/20080122/1200992629)