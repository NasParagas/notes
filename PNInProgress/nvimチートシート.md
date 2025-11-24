# cheet sheet


## `operator [number] motion`

### example

- To delete from the cursor up to the next word type:    "dw"

- To delete from the cursor to the end of a line type:   "d$"

- To delete a whole line type:                           "dd"

- To repeat a motion prepend it with a number:           "2w"

- "d2w"で二単語消せる
- 

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
- gg : ファイル先頭
- G  : ファイル後尾
    - [num] Gで指定行へ移動
- /  : 文字列検索
    - n : 次のマッチング
    - N : 前のマッチング
    - 次の検索をしない限り残り続ける
- <C-o> : 直前にいた場所に戻る
- % : かっこのペアのところへ移動
- s : 置換
    例.../s/thee/the/g で、カーソル行の全ての"thee"を"the"に置換
    %s/thee/the/gでファイル全体を対象にする
    gなしだとその行の最初のマッチングだけを置換
    
