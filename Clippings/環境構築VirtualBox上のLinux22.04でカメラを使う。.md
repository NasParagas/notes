---
title: "[環境構築]VirtualBox上のLinux22.04でカメラを使う。"
source: "https://zenn.dev/rg687076/articles/8174f50cc2475a"
author:
  - "[[Zenn]]"
published: 2023-11-27
created: 2025-08-21
description:
tags:
  - "clippings"
---
[tech](https://zenn.dev/tech-or-idea)

## 前提

- WebカメラはPCに挿しておく。
- [Windows 11 でカメラ設定を使用してカメラを管理する](https://support.microsoft.com/ja-jp/windows/windows-11-%E3%81%A7%E3%82%AB%E3%83%A1%E3%83%A9%E8%A8%AD%E5%AE%9A%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%97%E3%81%A6%E3%82%AB%E3%83%A1%E3%83%A9%E3%82%92%E7%AE%A1%E7%90%86%E3%81%99%E3%82%8B-97997ed5-bb98-47b6-a13d-964106997757) を参考に動作するかは確認しとく。

## 1.VirtualBox Extension PackをVirtualBoxに読み込む

## Oracle\_VM\_VirtualBox\_Extension\_Pack-xx.xx.xx.vbox-extpackをダウンロード

![](https://storage.googleapis.com/zenn-user-upload/ae02bd86f563-20231127.png)  
↓

## 拡張機能をインストール

![](https://storage.googleapis.com/zenn-user-upload/60841ec068b6-20231129.png)  
↓  
![](https://storage.googleapis.com/zenn-user-upload/a847cc9c5d69-20231127.png)  
↓  
![](https://storage.googleapis.com/zenn-user-upload/be4496f4b63c-20231127.png)  
↓  
![](https://storage.googleapis.com/zenn-user-upload/22117ff9ad5d-20231127.png)  
↓  
同意します。  
![](https://storage.googleapis.com/zenn-user-upload/d193fd27886e-20231127.png)  
↓  
完了。  
![](https://storage.googleapis.com/zenn-user-upload/cc131684f14a-20231127.png)

## 2.VMを起動させとく

![](https://storage.googleapis.com/zenn-user-upload/83aae0e06ebc-20231127.png)

## 3.Windowsで設定

## カメラ確認

Windowsコマンドプロンプト

```powershell
$ cd "C:\Program Files\Oracle\VirtualBox\"
$ VBoxManage list webcams

Video Input Devices: 1
.1 "HD Web Camera"
\\?\usb#vid_05a3&pid_9331&mi_00#7&7c239da&0&0000#{65e8773d-8f56-11d0-a3b9-00a0c9223196}\global
```

## VM確認

Windowsコマンドプロンプト

```powershell
$ VBoxManage list runningvms

"Ubt22_VSCode_Go_Webapp" {65d29d81-aa33-49dc-919e-38a16b8a4966}
```

## VMとカメラを関連付ける。

Windowsコマンドプロンプト

```powershell
$ VboxManage controlvm "Ubt22_VSCode_Go_Webapp" webcam attach .1
```

## 4.VMで、デバイス->Webカメラ->HD Web Cameraにチェック

![](https://storage.googleapis.com/zenn-user-upload/c3add0559f8e-20231127.png)

完了。これでVirtualBoxでWebカメラが使えるはず。  
※これって、VM起動の度に毎回設定する必要があるのね...。めんど。

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます