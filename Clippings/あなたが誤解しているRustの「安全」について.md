---
title: "あなたが誤解しているRustの「安全」について"
source: "https://opaupafz2.hatenablog.com/entry/2025/01/18/161717"
author:
  - "[[Ukicode (id:opaupafz2)]]"
published: 2025-01-18
created: 2025-09-01
description: "2025/2/6 動作がわかりやすいようにほぼすべてのコードに標準出力を追加 最近(2025/1/18現在)、チェスプログラムの評価を通して、Rustをボロクソに叩いた記事がZennに投稿された。 zenn.dev Rustはデバックモードでのスタックサイズが限られていて、これが非常に愚かだ。 安全な言語なのにスタックサイズに制限がある事は愚かだ。 こんな事を書いたら、後からクレームのコメントがバンバン来るだろうなとは思ってますが、この動画で私が共感したのは、Rustは糞だと言う事です。 一概に、Rustが早くて安全と言う事は、決してありません。 これについて、率直に感じたことは、「Rustの…"
tags:
  - "clippings"
---
- **2025/2/6**
	- **動作がわかりやすいようにほぼすべてのコードに標準出力を追加**

最近(2025/1/18現在)、チェ [スプログ](https://d.hatena.ne.jp/keyword/%A5%B9%A5%D7%A5%ED%A5%B0) ラムの評価を通して、Rustをボロクソに叩いた記事がZennに投稿された。  
[zenn.dev](https://zenn.dev/ohkan/articles/904b2b624304ff)  

> Rustはデバックモードでのスタックサイズが限られていて、これが非常に愚かだ。  
> 安全な言語なのにスタックサイズに制限がある事は愚かだ。

> こんな事を書いたら、後からクレームのコメントがバンバン来るだろうなとは思ってますが、この動画で私が共感したのは、 **Rustは糞だ** と言う事です。  
> 一概に、Rustが早くて安全と言う事は、決してありません。

これについて、率直に感じたことは、「Rustの『安全』は、あなたが思っているようなものではない」ということだ。そしてRustaceanも含め、この辺りは本当に誤解が多いと感じているため、今回は多くが誤解しているRustの「安全」について書くことにした。

### 未定義動作を理解する

[Python](https://d.hatena.ne.jp/keyword/Python) やTypeScript、 [Java](https://d.hatena.ne.jp/keyword/Java) といった高級すぎる言語をメインに書いている人にとってはあまり馴染みがないことだが、ハイパフォーマンスなC, [C++](https://d.hatena.ne.jp/keyword/C%2B%2B) には **未定義動作** があるということを知っておかなければならない。

未定義動作とは、たとえばISO/IEC 9899:2018(通称C17)では、以下のように定義されている(3.4.3 p1)。

> behavior, upon use of a nonportable or erroneous program construct or of erroneous data, for which this document imposes no requirements
> 
>   
> 
> (移植性がないもしくは誤ったプログラム構成、または誤ったデータを使用したときの動作で、本文書のいかなる要件を満たしていないもの)

また、未定義動作は、以下の場合に発生するとある(4 p2)。

> If a "shall" or "shall not" requirement that appears outside of a constraint or runtime-constraint is violated, the behavior is undefined. Undefined behavior is otherwise indicated in this document by the words "undefined behavior" or by the omission of any explicit definition of behavior. There is no difference in emphasis among these three; they all describe "behavior that is undefined".
> 
>   
> 
> (制約または実行時制約の外側に現れる「～すべき」または「～してはならない」要件が違反される場合、その動作は未定義である。未定義動作は、この文書ではそれ以外にも「undefined behavior」という言葉、または動作の明示的な定義の省略によって示す。これら3つの間に強調部分の違いはなく、これらはすべて「未定義である動作」を記述している。)

したがって、未定義動作は、 **「～すべき」または「～してはならない」を違反した場合の動作を指す** のであるから、未定義動作を含むコードは書くべきではない。

では、具体的にはどういったものが未定義動作となるのか、例を見ていこう。まずは、有名な配列の領域外アクセスである。

```cpp
#include <stdio.h>

int main(void)
{
    int foo[10] = {0};
    
    printf("foo[10] = %d\n", foo[10]);  // Undefined Behavior
    
    return 0;
}
```

次に、ポインタへの不正なアクセス。これはダングリングポインタと呼ばれる危険な行為である。

```cpp
#include <stdio.h>

int main(void)
{
    int *foo = NULL;
    {
        int bar = 42;
        foo = &bar;
    }
    
    printf("*foo = %d\n", *foo);    // Undefined Behavior
    
    return 0;
}
```

これ以外にも、未初期化変数へのアクセスや、データ競合、理解されにくいところだと [Strict Aliasing Rules](https://opaupafz2.hatenablog.com/entry/2024/03/31/120001) 違反も未定義動作となる。

実際のところ、未定義動作となるコードは、基本書いてはいけないコードばかりである。

ではそれを気を付ければ良いのでは？という話であるが、それはそうなのだが、常に意識し続けるのも大変であり、特に、チーム開発となると、未定義動作に対する意識が低いC, [C++](https://d.hatena.ne.jp/keyword/C%2B%2B) [プログラマ](https://d.hatena.ne.jp/keyword/%A5%D7%A5%ED%A5%B0%A5%E9%A5%DE) が一定数おり、それを指摘することで解決できるなら良いが、頑固な人は、わが道を突き進んで未定義動作を踏もうとするので、本当に厄介極まりない。

### Rustの「安全」について理解する

Rustは、こういった未定義動作となるような箇所を、 [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーでほぼすべてカバーした言語である。

まず、Rustでは参照先が解放済みである参照へのアクセスは [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーとなるのでダングリングポインタが発生しない。

```rust
fn main() {
    let foo: &i32;
    {
        let bar = 42;
        foo = &bar;
    }
    
    println!("*foo = {}", *foo);    // コンパイルエラー
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=08fd7f2d78c6ffffb8b592a394c8b3e9)

なぜダングリングポインタを防げるか

さらに驚くべきことに、Rustでは **データ競合も [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーで弾く** ことができる。

```rust
fn main() {
    use std::thread;
    
    let mut foo = false;
    thread::scope(|s| {
        let th1 = s.spawn(|| {
            foo = true;
            println!("th1: {foo}");
        });
        let th2 = s.spawn(|| {
            println!("th2: {foo}"); // \`th1\`で可変参照しているので参照できず
                                    // コンパイルエラー
        });
        
        th1.join().unwrap();
        th2.join().unwrap();
    });
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=8e98ceaf5cf7064bf6e031cd7d836e29)

なぜデータ競合を防げるか

先ほど「 [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーでほぼすべてカバー」と書いたが、それはカバーできていない部分が「安全」でないからではない。  
ここまでわざと書かなかったが、配列の領域外アクセスに関しては、静的に検査するのが技術的に難しく [\*2](https://opaupafz2.hatenablog.com/entry/2025/01/18/#f-95dff4aa "実現する方法は判明しているものの、まだ研究段階であり、導入している言語も少ない") 、Rustでも [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーとすることはできなかった。代わりに、 **アクセスするごとに境界チェックを行い、領域外ならパニックとすることで解決している** 。

```rust
fn main() {
    use core::array;
    
    let foo: [i32; 10] = array::from_fn(|i| i as i32);
    for i in 0..=10 {    // \`i\`は0～「10」の範囲なので領域外アクセスでパニック
        println!("foo[{i}] = {}", foo[i]);
    }
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=9373a2472d94ccc17002753973dc77f4)

これは実行時コストが生じてしまうのだが、実はRustでは上記の方法で配列を走査するのは一般的ではなく、それ以外の、安全かつ実行コストのない方法を用いるため、実用上において境界チェックが [ボトルネック](https://d.hatena.ne.jp/keyword/%A5%DC%A5%C8%A5%EB%A5%CD%A5%C3%A5%AF) となることはまずないだろう。

### Rustの「安全」で防げなかったもの

ここまで、Rustがいかに「安全」であることを書いてきたが、実はRustの「安全」では保障できないことがある。

Rustの「安全」で防げない例として、まずは [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が挙げられる。これは何らかの要因によりメモリが解放されずそのまま残り続ける現象である。 **ちょっと待って** 、Rustには所有権システムがあるのだから、 [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) なんて発生しないのでは？  
しかし、残念ながら **Rustでも [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) は発生する** のだ。代表的なのが、 `RefCell<Rc<T>>` を使ったリスト構造による循環参照である。このリスト構造で循環参照をすると、値を破棄しても参照カウント [\*3](https://opaupafz2.hatenablog.com/entry/2025/01/18/#f-f2e0d56a "Rcとは、まさしく参照カウント方式(Reference counted)のことを指している") が0になることはないため、結果として [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) となってしまうのである。  
また、循環参照なんてしなくても、Rustでは容易に [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) を発生させることが可能である。そのための関数が `mem::forget()` である。これは後述する `unsafe` がなくとも呼び出すことのできる、「安全」な関数である。

```rust
fn main() {
    use std::mem;
    
    let foo = Box::new(42); // ヒープ上に割り当て
    mem::forget(foo);       // これでメモリリークを起こせるが、これは「安全」である
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=7020d8203492af7b4891fe77b2e22e78)

とは言え、Rustでは **所有権システムにより、 [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が起こりにくい設計にはなっている** 。少なくとも、 `mem::forget()` を使った [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) は、意図的に起こさない限り起こらないことは明白である。故に、「Rustでは絶対に [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が起こらない」は偽であるが、同時に「Rustでは [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) が起こりやすい」もまた、偽なのである。

次に例として挙げられるのは、 [デッドロック](https://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF) である。一部の同期プリミティブは、ロック機構を用いるため、 **[デッドロック](https://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF) が起こる可能性がある** 。

```rust
fn main() {
    use std::sync::Mutex;
    
    let foo = Mutex::new(42);       // ミューテックス
    
    let bar = foo.lock().unwrap();
    println!("mutex lock: bar");
    
    let baz = foo.lock().unwrap();  // これでデッドロックになるが、これも「安全」である
    println!("mutex lock: baz");
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=c02345e234feb88ef1e06f8791026fe9) (プログラムを停止する場合、一番下にある入力欄でCtrl-Cを打つこと)

ここまでRustの「安全」で防げなかったものの例を書いてきたが、 **Rustにおいては、これらも「安全」なのである** 。ではなぜ「安全」なのか？  
答えは単純で、 [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) も [デッドロック](https://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF) も、 **プログラムのロジックに問題があるのであり、未定義動作ではない** からである。Rustにおいて「安全」であるとは、 **あくまで「未定義動作が発生しないことを保障する」ものでしかない** 。

確かに、 [メモリリーク](https://d.hatena.ne.jp/keyword/%A5%E1%A5%E2%A5%EA%A5%EA%A1%BC%A5%AF) や [デッドロック](https://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF) などが起こらないことも「安全」と定義しているところもある。しかし、「未定義動作が起こらない」という点では、Rustは「安全」なのである。

### 「Rustのunsafeは『安全』である」という詭弁

ここまで書いてきたことは、すべて **安全な** Rustに関してである。実はRustでは、「安全」を一時的に解除する「 [バックドア](https://d.hatena.ne.jp/keyword/%A5%D0%A5%C3%A5%AF%A5%C9%A5%A2) 」的な機能もある。

それが `unsafe` である。

Rustで `unsafe` コードを書く場合、それは **未定義動作を書いてしまうかもしれない** ことを示している。つまり `unsafe` は、文字通り「安全」ではない。

ここで勘違いしないでほしいのが、これは未定義動作コードを許容するための機能ではないことである。やむを得ない事情で、 **[プログラマ](https://d.hatena.ne.jp/keyword/%A5%D7%A5%ED%A5%B0%A5%E9%A5%DE) 自身が「安全」を保障する必要がある場合に使う** 機能である。

先ほど挙げた領域外アクセス、ダングリングポインタも、 `unsafe` を使えば起こすことができる。

```rust
fn main() {
    use core::array;
    
    let foo: [i32; 10] = array::from_fn(|i| i as i32);
    for i in 0..=10 {   // \`i\`は0～「10」の範囲なので領域外アクセスでUndefined Behavior
        unsafe {
            println!("foo[{i}] = {}", foo.get_unchecked(i));
        }
    }
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=release&edition=2021&gist=ba1d4da222dca8c15c097f68703da540)  

```rust
fn main() {
    let foo: *const i32;
    {
        let bar = 42;
        foo = &bar;
    }
    
    unsafe {
        println!("*foo = {}", *foo);    // Undefined Behavior
    }
}
```

[Rust Playgroundで実行](https://play.rust-lang.org/?version=stable&mode=release&edition=2021&gist=8242c13ef0afcf909defa52b95e9ad51)

無論、 `unsafe` は極力使わないで書くことが望ましいが、Rustが保証した「安全」の代償で、「安全」なコードも「安全」でないとして [コンパイル](https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB) エラーになることがあるため、それを「安全」な [API](https://d.hatena.ne.jp/keyword/API) として提供する際によく使われている印象である。  
その最たる例が、内部可変性パターンを用いた型である。これらは内部で `unsafe` を用いるが、使用する側はそれを意識することなく、「安全」に使用することができる。

ところで、この `unsafe` に関して、「 `unsafe` は安全だ」と主張してくる人が一定数存在している。  
だが、 `unsafe` は「未定義動作を踏む可能性がある」以上、「安全」ではないので、そのような主張はまったくもって詭弁である。この主張がまかり通ることは、特にチーム開発の面で悪影響が出ると私は考えているため、それを見た人は信じないでほしいし、主張する人も、それは本当にやめていただきたい所存である。

[\*1](https://opaupafz2.hatenablog.com/entry/2025/01/18/#fn-d74b3dc7):不変に生成しても、内部的に値を変えることのできる性質を持つオブジェクトを指す

[\*2](https://opaupafz2.hatenablog.com/entry/2025/01/18/#fn-95dff4aa):実現する方法は判明しているものの、まだ研究段階であり、導入している言語も少ない

[\*3](https://opaupafz2.hatenablog.com/entry/2025/01/18/#fn-f2e0d56a):Rcとは、まさしく参照カウント方式(Reference counted)のことを指している

[C言語で定数に型を付ける方法 »](https://opaupafz2.hatenablog.com/entry/2024/09/14/203929)