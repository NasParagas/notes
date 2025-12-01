# cheet sheet


## `operator [number] motion`

### example

- To delete from the cursor up to the next word type:    "dw"
- To delete from the cursor to the end of a line type:   "d$"
- To delete a whole line type:                           "dd"
- To repeat a motion prepend it with a number:           "2w"
- "d2w"で二単語消せる 

### operator

- h,j,k,l : move
- d       : delete
- c       : 差し替え(change)
    - d->iみたいな感じ?

### motion

- w : 直後の単語の先頭
- b : 直前の単語の先頭
- e : 直後の単語の後尾
- $ : 行尾
- 0 : 行尾


## other

- dd : 削除してレジスタへしまう
- U  : 行単位でundo
- p  : カーソルがある次の行へpaste
- P  :               前の行へ
- r  : 次に押した文字でreplace
- R  : 以降の文字をリプレイス
- gg : ファイル先頭
- G  : ファイル後尾
    - [num] Gで指定行へ移動
- /  : 文字列検索
    - n : 次のマッチング
    - N : 前のマッチング
    - 次の検索をしない限り残り続ける
- <C-o> : 直前にいた場所に戻る
- % : かっこのペアのところへ移動
- v : start visual section
      範囲選択みたいなのができる


## commands

### `:`
- :s : 置換(substitute)
    :s/old/new
        カーソル行の最初のoldをnewに置換
    :s/old/new/g 
        カーソル行の全て
    :#,#s/old/new/g
        指定範囲行の全て
    :%s/old/new/g
        ファイル全体
- :! : `ls`とかのコマンドがうてる
- :r : retrieve content
       ex) :r README.md
           :r !ls
- :! e<C-d>
    コマンド補完
- :e<Tab>
    neovim command completion
    <C-n>, <C-p> go next and go previous

### `/`
- /word :  search word
    :set noic  : ignoring case
    :set invic  : 多分設定を戻せる
```
 7. Prepend "no" to switch an option off:
~~~ cmd
        :set noic
~~~
 8. Prepend "inv" to invert an option:
~~~ cmd
        :set invic
~~~
```

## other

- <space>sh
    - search help doc
