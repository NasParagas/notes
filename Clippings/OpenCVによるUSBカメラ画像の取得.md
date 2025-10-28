---
title: "OpenCVによるUSBカメラ画像の取得"
source: "https://qiita.com/vs4sh/items/4a9ce178f1b2fd26ea30"
author:
  - "[[vs4sh]]"
published: 2015-07-16
created: 2025-08-21
description: "OpenCVによるUSBカメラ画像の取得 OpenCVでは，cv::VideoCaptureで，USB接続したカメラから簡単に画像を取得することができる． 以下では，公式サンプルを一部改変したコードで説明していく． 基本的な手順は カメラデバイスのオープン． カメラ画像..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

OpenCVでは， `cv::VideoCapture` で，USB接続したカメラから簡単に画像を取得することができる．

以下では， [公式サンプル](http://docs.opencv.org/3.0-beta/modules/videoio/doc/reading_and_writing_video.html) を一部改変したコードで説明していく．

基本的な手順は

1. カメラデバイスのオープン．
2. カメラ画像の取得と画面への描画．
3. デバイスの解放．

```cpp
# include "opencv2/opencv.hpp"

int main(int argh, char* argv[])
{
    cv::VideoCapture cap(0);//デバイスのオープン
    //cap.open(0);//こっちでも良い．
    
    if(!cap.isOpened())//カメラデバイスが正常にオープンしたか確認．
    {
        //読み込みに失敗したときの処理
        return -1;
    }

    cv::Mat frame; //取得したフレーム
    while(cap.read(frame))//無限ループ
    {
        //
        //取得したフレーム画像に対して，クレースケール変換や2値化などの処理を書き込む．
        //

        cv::imshow("win", frame);//画像を表示．
        const int key = cv::waitKey(1);
        if(key == 'q'/*113*/)//qボタンが押されたとき
        {
            break;//whileループから抜ける．
        }
        else if(key == 's'/*115*/)//sが押されたとき
        {
            //フレーム画像を保存する．
            cv::imwrite("img.png", frame);
        }
    }
    cv::destroyAllWindows();
    return 0;
}
```

## USBカメラのオープン

- `cv::VideoCapture` を用いて，USBカメラにアクセスを行う．
- `cv::VideoCapture::open()` の引数によって，開くカメラデバイスを選択する．
	- 筆者の環境(Macbook Air)では，内蔵インカメラが0で，USBで接続したカメラに1以降が割り当てられている．
- USBカメラが正常にオープン出来たかはisOpen()を呼ぶことで確認できる．

```cpp
cv::VideoCapture cap(0);//デバイスのオープン
//cap.open(0);//こっちでも良い．
    
if(!cap.isOpened())//カメラデバイスが正常にオープンしたか確認．
{
    //読み込みに失敗したときの処理
    return -1;
}
```

## USBカメラ画像の表示

- カメラ画像を連続して取得する場合などは，while文やfor文などのループ中に，画像の取得や描画処理を書き， `cv::imshow()` で表示する．
- ある条件を満たしたらbreakを呼ぶようにすることでループから抜けて処理を終了する．
	- 以下のサンプルでは， `cv::waitKey()` で取得したキー入力を終了のための条件としている．

while文の中で行っている手順は以下の通り．

- `cv::VideoCapture::read()` で現在のフレームを `cv::Mat` に取得する．
	- `cv::VideoCapture::read()` はフレーム取得に成功すると， `true` ，失敗すると `false` を返すので，
	- 以下の例では，これをwhile文の継続条件としている．
- 取得した画像をcv::imshow()でウィンドウに描画する．
- cv::waitKey()でキー入力を受ける．
	- 引数は待ち時間(ms)．
	- 戻り値は押されたキーのアスキーコード．
- 取得したキーで終了や保存処理を行う．

```cpp
cv::Mat frame; //取得したフレーム
while(cap.read(frame))//無限ループ
{
    //
    //取得したフレーム画像に対して，クレースケール変換や2値化などの処理を書き込む．
    //

    cv::imshow("win", frame);//画像を表示．
    const int key = cv::waitKey(1);
    if(key == 'q'/*113*/)//qボタンが押されたとき
    {
        break;//whileループから抜ける．
    }
    else if(key == 's'/*115*/)//sが押されたとき
    {
       //フレーム画像を保存する．
       cv::imwrite("img.png", frame);
    }
}
cv::destroyAllWindows();// ウィンドウを閉じる．

return 0;
```

## デバイスの解放

- `cv::VideoCapture` のデストラクタが正常に呼ばれると自動的にデバイスは解放される．
- `cv::VideoCapture::release()` を呼ぶと明示的に解放することができる．

[0](https://qiita.com/vs4sh/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fvs4sh%2Fitems%2F4a9ce178f1b2fd26ea30&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fvs4sh%2Fitems%2F4a9ce178f1b2fd26ea30&realm=qiita)

[100](https://qiita.com/vs4sh/items/4a9ce178f1b2fd26ea30/likers)

いいねしたユーザー一覧へ移動

108