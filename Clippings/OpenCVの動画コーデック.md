---
title: "OpenCVの動画コーデック"
source: "https://qiita.com/Gyutan/items/e279e8680b90ca891250"
author:
  - "[[Gyutan]]"
published: 2020-06-20
created: 2025-08-26
description: "はじめに なめらかな動画をとるためには、フレームレートを上げる必要があります。 動画圧縮のコーデックによっては、フレームレートが上がらないことがあります。 そこで、OpenCVでのコーデック情報の取得と、コーデックの変更方法についてメモとして記載します。 参考： Open..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@Gyutan](https://qiita.com/Gyutan)

投稿日

### はじめに

なめらかな動画をとるためには、フレームレートを上げる必要があります。  
動画圧縮のコーデックによっては、フレームレートが上がらないことがあります。  
そこで、OpenCVでのコーデック情報の取得と、コーデックの変更方法についてメモとして記載します。

参考：  
[OpenCVのカメラ読み込みを高速化し、遅延時間も短くする](https://qiita.com/iwatake2222/items/b8c442a9ec0406883950)  
[OpenCVからWebカメラの「解像度」「FPS」及び、「フォーマット(コーディック)」を設定する方法](https://dev.classmethod.jp/articles/opencv-webcam-setting/)

### 動作環境

Win10 Pro 64 bit  
Python　3.7  
Anaconda  
OpenCV 4.2　（conda-forgeでインストール）

### 動画コーデック

動画を保存する際に、データ量が非常に大きいためにデータの圧縮を行います。この圧縮アルゴリズムがコーデックです。圧縮アルゴリズムにはいろいろあり、そのアルゴリズムに基づいて動画の拡張子（.mp4、.avi）が決まります。

参考：  
[動画コーデックをマスターしよう](https://www.boel.co.jp/tips/vol43_prores.html)  
[【コーデック】とは？動画・音声コーデックそしてコンテナ(動画形式)](https://bestplanner.co.jp/smartcamera/codec/)

### 動画コーデックの種類

代表的なものとしては、  
H.264（.mp4）･･･ 高画質動画の主流となっている規格  
MPEG-2（.mpg）･･･テレビのデジタル放送を中心に利用  
MPEG-4（.mpg.mp4）･･･モバイル機器でのネット動画の視聴を想定し開発された規格  
など

参考：  
[OpenCVのVideoWriterを使って画像から動画を作る。](http://shibafu3.hatenablog.com/entry/2016/11/13/151118)  
[動画コーデックの種類と違い(H.264･VP9･MPEG･Xvid･DivX･WMV等)【比較】](https://aviutl.info/ko-dekku-tigai/)

### OpenCVでカメラのコーデックを調べる

OpenCVでは、カメラの設定条件を調べたり、機種によっては設定することができます。

参考：  
[Python, OpenCVで動画を読み込み（ファイル・カメラ映像）](https://note.nkmk.me/python-opencv-videocapture-file-camera/)  
[Pythonでカメラを制御する【研究用】](https://qiita.com/opto-line/items/7ade854c26a50a485159)

#### OpenCVにおけるコーデックの確認方法

```python
import cv2

cap = cv2.VideoCapture(0)    #カメラ番号を指定

# 戻り値はDuble型なのでInt型に変換する
fourcc_int = int(cap.get(cv2.CAP_PROP_FOURCC))
cap.release()

print("fourcc_int",fourcc_int)
# >>>fourcc_int 842094158

# Four codec characterに変換する方法1
fourcc = list((fourcc_int.to_bytes(4, 'little').decode('utf-8')))
print(fourcc)
# >>>['N', 'V', '1', '2']

print("fourcc_string",fourcc_int.to_bytes(4, 'little').decode('utf-8'))
# >>>fourcc_string NV12

# Four codec charactersに変換する方法2
fourcc = [chr((fourcc_int >> 8 * i) & 0xFF) for i in range(4)]
print(fourcc)
# >>>['N', 'V', '1', '2']

print("fourcc_string", "".join([chr((fourcc_int >> 8 * i) & 0xFF) for i in range(4)]))
# >>>fourcc_string NV12
```

#### Four codec文字列変換の解説

```text
"""
詳解 OpenCV 3 ―コンピュータビジョンライブラリを使った画像処理・認識
によると以下のようなビットの並びになる。

char fourcc[] ={
                (char)f,              #最初の文字0-7ビット
                (char)(f >>8),         #次の文字8-15ビット
                (char)(f >>16),     #次の文字16-23ビット
                (char)(f >>24),     #次の文字24-31ビット
                '\0'                #'\0'で終了
                }
"""
```

##### 方法1

参考：  
[Binary変換](https://docs.python.org/ja/3/library/stdtypes.html)  
[文字列、Byte、Binary](https://qiita.com/Gyutan/items/ce001f1f811ecc4d1960)

```python
# 整数を表すバイト列を返す。4文字なので4Byte
# byteorder が "little" なので、最上位のバイトがバイト配列の最後に来る。
print("fourcc_Byte",fourcc_int.to_bytes(4, 'little'))
# >>>fourcc_Byte b'NV12'

# utf-8でdecodeして文字列に変換
print('utf-8',fourcc_int.to_bytes(4, 'little').decode('utf-8'))
# >>>utf-8 NV12

# 最後にまとめると
fourcc = fourcc_int.to_bytes(4, 'little').decode('utf-8')
print(fourcc)
# >>>NV12

# 整数型を2進数で表してみます。
print("fourcc_binary",bin(fourcc_int))
# >>>fourcc_binary 0b110010001100010101011001001110

# binaryで確認してみる
print(bin(ord("N")))
# >>>0b1001110
print(bin(ord("V")))
# >>>0b1010110
print(bin(ord("1")))
# >>>0b110001
print(bin(ord("2")))
# >>>0b110010
print(bin(ord("2")),bin(ord("1")),bin(ord("V")),bin(ord("N")))
# >>>0b110010 0b110001 0b1010110 0b1001110　#最初の0bは2進数の意味
print(bin(fourcc_int))
# >>>0b 110010 00110001 01010110 01001110
```

##### 方法２

参考：  
[https://github.com/opencv/opencv/blob/master/samples/python/video\_v4l2.py](https://github.com/opencv/opencv/blob/master/samples/python/video_v4l2.py)  
[ビット演算子](https://www.grapecity.com/developer/support/powernews/column/clang/019/page02.htm)  
[Python ビット演算 超入門](https://qiita.com/7shi/items/41d262ca11ea16d85abc)

```python
print((fourcc_int >> 8 *0)& 0xFF)
# >>>78 -> N

"""
x >> n　xのn ビット右シフト
x & y　xとyのビット単位論理積
0xFF：255 の16進数（8Bit＝1Byte）
example:
'N' 0b01001110
01001110
   &
11111111
--------
01001110
2+4+8+64=78

"""

# N = 78 か確認
print(ord('N'))
# >>>78
print((fourcc_int >> 8 *1)& 0xFF)
# >>>86 -> V

# List内包表現
print([chr((fourcc_int >> 8 * i) & 0xFF) for i in range(4)])
# >>>['N', 'V', '1', '2']
# 文字列形式に変換
print("".join([chr((fourcc_int >> 8 * i) & 0xFF) for i in range(4)]))
# >>>NV12
```

NV21は、非圧縮フォーマットなので解像度の大きな映像を高フレームレートで転送するのには向きません。  
参考：  
[Webカメラからh264動画を取得した〜い〜その２〜](https://serenegiant.com/blog/?p=2937)  
[4:2:0 ビデオ ピクセル形式](https://docs.microsoft.com/ja-jp/windows-hardware/drivers/display/4-2-0-video-pixel-formats)

ちなみに、OpenCVでカメラ撮影を抜けるときに使う以下の書き方もビット演算です。

```python
if cv2. waitKey (0) & 0xFF == ord ('q'):
```

参考：  
[python - str - cv2.waitKey(1)の0xFFは何ですか?](https://code.i-harness.com/ja-jp/q/21bbe9c)

### OpenCVにおけるコーデックの設定方法

参考：  
[USBカメラのMotion-JPEG を Python + OpenCV　+ VideoCapture でキャプチャーする方法](https://jitaku.work/it/image-processing/opencv/videocapture-mjpg/)

```python
import cv2

cap = cv2.VideoCapture(0)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1920)        #カメラの仕様を確認
cap.set(cv2.CAP_PROP_FRAME_HEIGHT,1080)        #カメラの仕様を確認

cap.set(cv2.CAP_PROP_FOURCC, cv2.VideoWriter_fourcc(*"MJPG"))    #カメラの仕様を確認
# cap.set(cv2.CAP_PROP_FOURCC, cv2.VideoWriter_fourcc('M','J','P','G'))
# *"MJPG"または、'M','J','P','G'は同じです。

cap.set(cv2.CAP_PROP_FPS, 30)    #カメラの仕様を確認

fourcc = int(cap.get(cv2.CAP_PROP_FOURCC))
fourcc = fourcc.to_bytes(4, 'little').decode('utf-8')
print("FOURCC=" + str(fourcc))

fps    = int(cap.get(cv2.CAP_PROP_FPS))
print("FPS=" + str(fps))

cap.release()
```

コーデック名 引数 拡張子  
MP4S ('M', 'P', '4', 'S').mp4  
MP4V ('M', 'P', '4', 'V').mp4  
DIV3 ('D', 'I', 'V', '3').avi  
DIVX ('D', 'I', 'V', 'X').avi  
IYUV ('I', 'Y', 'U', 'V').avi  
MJPG ('M', 'J', 'P', 'G').avi  
XVID ('X', 'V', 'I', 'D').avi  
H263 ('H', '2', '6', '3').wmv  
mp4v ('m', 'p', '4', 'v').mov

#### 動画を直接撮影してフレームレート測定

参考：  
[Python, OpenCVで動画再生時のFPS（フレームレート）を測定・表示](https://note.nkmk.me/python-opencv-fps-measure/)

```python
import datetime
import cv2

def movie_capture_save_fps(camera_number=0,set_time=5):
    """
    カメラのFPS測定用 Set_timeの間動画を撮り続けその時のfpsをコンソールに出力
    
    saved file name：year_month_day_hour_minutes_second.mp4

    :param 
    camera_number: camera number. Default  0
    set_time：measurement time unit second  default 5s
    
    *** ffmpeg install or opencv version over 4.0
    
    """

    now = datetime.datetime.now()
    
    # 注意コーデックによっては、保存形式が異なる場合があるのでその時は変更する。
    filename = now.strftime('%y_%m_%d_%H_%M_%S') + '.mp4'
    # filename = now.strftime('%y_%m_%d_%H_%M_%S') + '.avi'
    
    # Capture image
    cap = cv2.VideoCapture(camera_number) 
    # cap = cv2.VideoCapture(camera_number, cv2.CAP_DSHOW)
    
    #camera info
    fourcc = int(cap.get(cv2.CAP_PROP_FOURCC))
    fps = int(cap.get(cv2.CAP_PROP_FPS))                    # カメラのFPSを取得
    w = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))              # カメラの横幅を取得
    h = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))             # カメラの縦幅を取得
    
    print("Current: fourcc:{} fps:{}　width:{}　height:{}".format(fourcc.to_bytes(4, 'little').decode('utf-8'), fps, w, h))

    #  Set Codec, fps  
    fourcc = cv2.VideoWriter_fourcc('m', 'p', '4', 'v') 
    cap.set(cv2.CAP_PROP_FOURCC, fourcc)
    # # fourcc = cv2.VideoWriter_fourcc('a', 'v', 'c', '1') 
    # # fourcc = cv2.VideoWriter_fourcc('H', '2', '6', '4')      
    # # fourcc = cv2.VideoWriter_fourcc('Y','U','Y','V')          
    fps = cap.set(cv2.CAP_PROP_FPS,30)
    
    out = cv2.VideoWriter(filename, fourcc, fps, (w ,h))
    tm = cv2.TickMeter() # fps測定用
    tme = cv2.TickMeter() #Set_time測定用
    tm.start()
    tme.start()
    count = 0
    max_count = 10
    fps = 0
    elasp_time=0
    
    
    while (cap.isOpened()):
        ret, frame = cap.read()
        # Resize the window
        # windowsize = (800, 600)
        # frame = cv2.resize(frame, windowsize)

        if ret is True:

            # Display the resulting frame
            # cv2.imshow('frame', frame)
            
            # Write the frame
            out.write(frame)
            tme.stop()
            elasp_time = tme.getTimeSec()
            tme.start()
            
            if count == max_count:
                tm.stop()
                time_el = tm.getTimeSec()
                fps = max_count / time_el
                elasp_time = elasp_time + time_el
                tm.reset()
                tm.start()
                count = 0
                
            print('FPS: {:.2f}, 1/FPS[ms]: {:.2f}, movie:{:.2f} '.format(fps,(1/(fps+0.01))*1000,elasp_time))
            #1/(fps+0.01)は0で割る止まってしまうため便宜上fps+0.01としている。
            
            count += 1
            
            # if cv2.waitKey(1) & 0xFF == ord('q'):
            #     print('break')
            #     break
            
            if elasp_time > set_time:
                print('Break by Time out')
                ret = False
                break
        else:
            break

    # Release everything if job is finished
    tm.stop()
    tm.reset()
    tme.stop()
    tme.reset()
    cap.release()
    out.release()
    cv2.destroyAllWindows()
    
if __name__ == '__main__':
    movie_capture_save_fps(camera_number=0,set_time=5)
```

### その他　（関連情報）

##### VideoCapturePropertiesの一覧

OpenCV document  
[https://docs.opencv.org/4.2.0/d4/d15/group\_\_videoio\_\_flags\_\_base.html#gaeb8dd9c89c10a5c63c139bf7c4f5704d](https://docs.opencv.org/4.2.0/d4/d15/group__videoio__flags__base.html#gaeb8dd9c89c10a5c63c139bf7c4f5704d)

| 引数 | 説明 |
| --- | --- |
| cv.CAP\_PROP\_POS\_MSEC | Current position of the video file in milliseconds. |
| cv.CAP\_PROP\_POS\_FRAMES | 0-based index of the frame to be decoded/captured next. |
| cv.CAP\_PROP\_POS\_AVI\_RATIO | Relative position of the video file: 0=start of the film, 1=end of the film. |
| cv.CAP\_PROP\_FRAME\_WIDTH | Width of the frames in the video stream. |
| cv.CAP\_PROP\_FRAME\_HEIGHT | Height of the frames in the video stream. |
| cv.CAP\_PROP\_FPS | Frame rate. |
| cv.CAP\_PROP\_FOURCC | 4-character code of codec. see [VideoWriter::fourcc](https://docs.opencv.org/4.2.0/dd/d9e/classcv_1_1VideoWriter.html#afec93f94dc6c0b3e28f4dd153bc5a7f0). |
| cv.CAP\_PROP\_FRAME\_COUNT | Number of frames in the video file. |
| cv.CAP\_PROP\_FORMAT | Format of the Mat objects (see [Mat::type()](https://docs.opencv.org/4.2.0/d3/d63/classcv_1_1Mat.html#af2d2652e552d7de635988f18a84b53e5)) returned by [VideoCapture::retrieve()](https://docs.opencv.org/4.2.0/d8/dfe/classcv_1_1VideoCapture.html#a9ac7f4b1cdfe624663478568486e6712). Set value -1 to fetch undecoded RAW video streams (as [Mat](https://docs.opencv.org/4.2.0/d3/d63/classcv_1_1Mat.html) 8UC1). |
| cv.CAP\_PROP\_MODE | Backend-specific value indicating the current capture mode. |
| cv.CAP\_PROP\_BRIGHTNESS | Brightness of the image (only for those cameras that support). |
| cv.CAP\_PROP\_CONTRAST | Contrast of the image (only for cameras). |
| cv.CAP\_PROP\_SATURATION | Saturation of the image (only for cameras). |
| cv.CAP\_PROP\_HUE | Hue of the image (only for cameras). |
| cv.CAP\_PROP\_GAIN | Gain of the image (only for those cameras that support). |
| cv.CAP\_PROP\_EXPOSURE | Exposure (only for those cameras that support). |
| cv.CAP\_PROP\_CONVERT\_RGB | Boolean flags indicating whether images should be converted to RGB. |
| cv.CAP\_PROP\_WHITE\_BALANCE\_BLUE\_U | Currently unsupported. |
| cv.CAP\_PROP\_RECTIFICATION | Rectification flag for stereo cameras (note: only supported by DC1394 v 2.x backend currently). |
| cv.CAP\_PROP\_MONOCHROME |  |
| cv.CAP\_PROP\_SHARPNESS |  |
| cv.CAP\_PROP\_AUTO\_EXPOSURE | DC1394: exposure control done by camera, user can adjust reference level using this feature. |
| cv.CAP\_PROP\_GAMMA |  |
| cv.CAP\_PROP\_TEMPERATURE |  |
| cv.CAP\_PROP\_TRIGGER |  |
| cv.CAP\_PROP\_TRIGGER\_DELAY |  |
| cv.CAP\_PROP\_WHITE\_BALANCE\_RED\_V |  |
| cv.CAP\_PROP\_ZOOM |  |
| cv.CAP\_PROP\_FOCUS |  |
| cv.CAP\_PROP\_GUID |  |
| cv.CAP\_PROP\_ISO\_SPEED |  |
| cv.CAP\_PROP\_BACKLIGHT |  |
| cv.CAP\_PROP\_PAN |  |
| cv.CAP\_PROP\_TILT |  |
| cv.CAP\_PROP\_ROLL |  |
| cv.CAP\_PROP\_IRIS |  |
| cv.CAP\_PROP\_SETTINGS | Pop up video/camera filter dialog (note: only supported by DSHOW backend currently. The property value is ignored) |
| cv.CAP\_PROP\_BUFFERSIZE |  |
| cv.CAP\_PROP\_AUTOFOCUS |  |
| cv.CAP\_PROP\_SAR\_NUM | Sample aspect ratio: num/den (num) |
| cv.CAP\_PROP\_SAR\_DEN | Sample aspect ratio: num/den (den) |
| cv.CAP\_PROP\_BACKEND | Current backend (enum VideoCaptureAPIs). Read-only property. |
| cv.CAP\_PROP\_CHANNEL | Video input or Channel Number (only for those cameras that support) |
| cv.CAP\_PROP\_AUTO\_WB | enable/ disable auto white-balance |
| cv.CAP\_PROP\_WB\_TEMPERATURE | white-balance color temperature |
| cv.CAP\_PROP\_CODEC\_PIXEL\_FORMAT | (read-only) codec's pixel format. 4-character code - see [VideoWriter::fourcc](https://docs.opencv.org/4.2.0/dd/d9e/classcv_1_1VideoWriter.html#afec93f94dc6c0b3e28f4dd153bc5a7f0). Subset of [AV\_PIX\_FMT\_\*](https://github.com/FFmpeg/FFmpeg/blob/master/libavcodec/raw.c) or -1 if unknown |

##### 動画ファイルのコーデックを調べる方法

[MediaInfoOnline](https://mediaarea.net/MediaInfoOnline)

[0](https://qiita.com/Gyutan/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FGyutan%2Fitems%2Fe279e8680b90ca891250&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FGyutan%2Fitems%2Fe279e8680b90ca891250&realm=qiita)

[34](https://qiita.com/Gyutan/items/e279e8680b90ca891250/likers)

いいねしたユーザー一覧へ移動

32