---
title: "Matを複製する"
source: "https://cvtech.cc/clone/"
author:
  - "[[Mochizuki]]"
published: 2016-07-27
created: 2025-08-21
description: "Matは単純にイコール記号でコピーを作ることができません。以下のようなコードで完全なコピーを作成できます。 #"
tags:
  - "clippings"
---
- [HOME](https://cvtech.cc/)
- [C++](https://cvtech.cc/type/cpp/)
- [C++ 便利技](https://cvtech.cc/type/cpp/technique/)
- Matを複製する

Matは単純にイコール記号でコピーを作ることができません。以下のようなコードで完全なコピーを作成できます。

```cpp
#include "opencv/cv.h"
#include "opencv/highgui.h"
int main(){
    Mat img1;
    Mat img2;
       :
       :
       :
    img2=img1.clone();
       :
       :
}
```

> cv::Matを代入演算子でコピーすると，浅いコピー（shallow copy）が行われ，コピー元とコピー先とcv::Matでデータが共有されます．この場合，最も大きいメンバであるデータ自身が実際にはコピーされないので，cv::Matのコピー動作はサイズに依存せず高速に行われます．それぞれのcv::Matのdataポインタは，同じメモリアドレスを指すので，コピー元cv::Matのデータを書き換えると，コピー先cv::Matのデータも変わることになります．
> 
> コピー元とコピー先を別データとしてコピーするには，clone()メソッドを利用して，深いコピー（deep copy）を行います． clone()メソッド内部では，新たなcv::Matが作成され，そこにcopyTo()メソッドでデータ全体がコピーされます．
> 
> \[[opencv.jp](http://opencv.jp/cookbook/opencv_mat.html#id3) より\]