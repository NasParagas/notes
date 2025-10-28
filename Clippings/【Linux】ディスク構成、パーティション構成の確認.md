---
title: "【Linux】ディスク構成、パーティション構成の確認"
source: "https://qiita.com/UKIUKI_ENGINEER/items/c7eece56b916d2a4afe9"
author:
  - "[[UKIUKI_ENGINEER]]"
published: 2022-03-25
created: 2025-08-26
description: "Linuxを触り始めたころ、 このマシンにはハードディスクが何本挿さっていて、パーティション分割はどうなっているのか？ sdaとかsdbとかsda1,sda2とか何なんだ？ などが個人的分かりづらいと思っていました。 初歩的な話ですが、自分の復習も兼ねてまとめます。 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

Linuxを触り始めたころ、

- このマシンにはハードディスクが何本挿さっていて、パーティション分割はどうなっているのか？
- sdaとかsdbとかsda1,sda2とか何なんだ？

などが個人的分かりづらいと思っていました。  
初歩的な話ですが、自分の復習も兼ねてまとめます。

## ディスク・パーティションの確認

```text
# ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sdb  /dev/sdb1  /dev/sdb2  /dev/sdb3  /dev/sdc  /dev/sdc1
```

- **sda,sdb...はLinuxにおけるハードディスクのデバイス名**  
	/devにsdaやsdbなどのファイルがあります。  
	これらが、 **挿さっているディスクを示すファイル** です。  
	このようにLinuxでは、 **HDDやUSBメモリなどの装置を/dev/sdaなどのデバイスファイルとして管理** します。  
	上の例では、sda、sdb、sdcがあるので、ディスクが3本挿さっていることになります。
- **sda1,sda2などの数字はパーティション番号**  
	sda1,sda2などの数字はパーティション番号です。  
	例えば上の例のようにsdb1~sdb3がある場合、sdbというディスクが3つのパーティションに分割されていることを意味します。  
	sdaとsdcはそれぞれsda1とsdc1があるので、パーティションは一つだけとなっていることが分かります。

## lsblkコマンド

ディスク構成、パーティション構成を確認するにはlsblkコマンドが便利です。  
下の例では、「lsblk -ip」と実行しています。ここで、「-i：ascii表示」、「-p：フルパス表示」です。  
ディスクが何本挿さっていて、それらがどのようにパーティション分割されているのか、論理ボリュームの構成、マウント先などが分かります。  
例えば、/dev/sdaはパーティションが1つで、それが論理ボリュームによって2つに区切られていることが分かります。  
論理ボリュームの内lv\_nasは、/mnt/usbhddにマウントされています。  
/dev/sdcはパーティションが1つで構成されています。sdc1はマウント先がありません。そのためsdcというディスクは挿さっているだけでどこにもマウントされていないことが分かります。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2524631/b1bf165c-b936-3546-f4c2-2dae23448337.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2524631%2Fb1bf165c-b936-3546-f4c2-2dae23448337.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=605cdf18cc935332683896a79e9d730e)

[1](https://qiita.com/UKIUKI_ENGINEER/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FUKIUKI_ENGINEER%2Fitems%2Fc7eece56b916d2a4afe9&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FUKIUKI_ENGINEER%2Fitems%2Fc7eece56b916d2a4afe9&realm=qiita)

[33](https://qiita.com/UKIUKI_ENGINEER/items/c7eece56b916d2a4afe9/likers)

いいねしたユーザー一覧へ移動

27