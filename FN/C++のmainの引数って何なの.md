# なんなんだ

- こんなの

```cpp
int main(int argc, char **argv) {}
```

## int main

- 返り値が`int`
- C++のプログラムは終了した場合にプログラムの終了コードをOSへ返す
  - 正常終了: 0
  - 異常終了: 0以外
- 実際には、main関数の`return 0;`が省略できるので省略しているのが多い
  - 明示的に以上ステータスを返したりもできるはず

## argc

- `argument count`
  - つまり引数の個数。最低でも1(プログラム名)
    - Rustもそんな感じだったな

## argv

- Copilot

```txt
argv（argument vector）
C 文字列（null終端の char*）の配列です。

argv[0]：通常は実行プログラムの名前/パス（環境によって空文字の場合もありえる）
argv[1]〜argv[argc-1]：ユーザーが渡した引数
argv[argc] は必ず nullptr（終端マーカー）
```

- こんな書き方もあるらしい

```cpp

char **argv      // 「char* の配列（配列の先頭ポインタ）」という意味
char *argv[]     // 上と等価な表現
const char *argv[] // こちらの方が“引数を変更しない”意図が明確でおすすめ
```
