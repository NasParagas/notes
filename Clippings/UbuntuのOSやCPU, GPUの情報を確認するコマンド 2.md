---
title: "UbuntuのOSやCPU, GPUの情報を確認するコマンド"
source: "https://qiita.com/sabaku20XX/items/97db2c0bf7298e3a645c"
author:
  - "[[sabaku20XX]]"
published: 2018-07-09
created: 2025-11-07
description: "この記事の内容 UbuntuのshellからOSの情報と、CPU, GPUのハードウェア情報を確認するコマンドの解説をします。 UbuntuにCUDAやcuDNN, tensorflowをインストールする時によく使うと思います。 概要 OSのバージョン $ ls..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## この記事の内容

- UbuntuのshellからOSの情報と、CPU, GPUのハードウェア情報を確認するコマンドの解説をします。
- UbuntuにCUDAやcuDNN, tensorflowをインストールする時によく使うと思います。

## 概要

- OSのバージョン

```text
$ lsb_release -a
```

- カーネル

```text
$ uname -a
```

- CPU

```text
$ sudo lshw -class processor
```

- GPU

```text
$ lspci | grep -i nvidia
```

## OSのバージョン

- OSのバージョンを確認します。
- `lsb_release` コマンドでOSのディストリビューションやバージョンが確認できます。

```text
$ lsb_release -a
No LSB modules are available.
Distributor ID:    Ubuntu
Description:    Ubuntu 18.04 LTS
Release:    18.04
Codename:    bionic
```

- 参照 man - lsb\_release
	- [http://manpages.ubuntu.com/manpages/bionic/man1/lsb\_release.1.html](http://manpages.ubuntu.com/manpages/bionic/man1/lsb_release.1.html)

## OSのカーネル

- `uname` コマンドで確認できます。
- 参照 man - uname
	- [http://manpages.ubuntu.com/manpages/artful/man1/uname.1.html](http://manpages.ubuntu.com/manpages/artful/man1/uname.1.html)

`-a` オプションを付けると出せる情報が全部でます。

```text
$ uname -a
Linux R7 4.15.0-23-generic #25-Ubuntu SMP Wed May 23 18:02:16 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

`--kernel-release` とか `--kernel-version` とかって付けると、項目別に表示されて見やすいです。

```text
$ uname --kernel-release
4.15.0-23-generic
$ uname --kernel-version
# 25-Ubuntu SMP Wed May 23 18:02:16 UTC 2018
```

## CPUのハード情報

- CPUの型番、コア数、論理コア数などを確認します。
- いくつかの方法があります。

### lshwで確認

- `lshw` コマンドでCPU以外も含めた様々なハードの情報が見れます。
- 参照 man - lshw:
	- [http://manpages.ubuntu.com/manpages/bionic/man1/lshw.1.html](http://manpages.ubuntu.com/manpages/bionic/man1/lshw.1.html)

何もオプションを付けない場合、以下のように様々な詳細な情報が表示されるのですが、長くて概要が掴みにくいです。

```text
$ sudo lshw
r7                          
    description: Desktop Computer
    product: Alienware Aurora R7 (0858)
    vendor: Alienware
    version: 1.0.8
    serial: 6P39KP2
    width: 64 bits
    capabilities: smbios-3.1 dmi-3.1 smp vsyscall32
...
```

- `-class processor` オプションを付けてCPUの情報だけに絞ってみます。
	- `product` からCPUの機種が `Intel(R) Core(TM) i7-8700 CPU @ 3.20GHz`
	- `configuration` から `cores=6 enabledcores=6 threads=12` と言った情報が分かります。

```text
$ sudo lshw -class processor
  *-cpu                     
       description: CPU
       product: Intel(R) Core(TM) i7-8700 CPU @ 3.20GHz
       vendor: Intel Corp.
       physical id: 36
       bus info: cpu@0
       version: Intel(R) Core(TM) i7-8700 CPU @ 3.20GHz
       serial: To Be Filled By O.E.M.
       slot: U3E1
       size: 2943MHz
       capacity: 4600MHz
       width: 64 bits
       clock: 100MHz
       capabilities: x86-64 fpu fpu_exception wp vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb invpcid_single pti ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm mpx rdseed adx smap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp cpufreq
       configuration: cores=6 enabledcores=6 threads=12
```

### /proc/cpuinfoで確認

- CPUの情報は `$cat /proc/cpuinfo` でも行えます。
- `/proc` ディレクトリはマシンやカーネルの情報を扱うための仮想的なファイルが収められたディレクトリです。
	- 参照: [http://refspecs.linuxfoundation.org/FHS\_3.0/fhs/ch06.html#procKernelAndProcessInformationVir](http://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch06.html#procKernelAndProcessInformationVir)

やってみましょう。

```text
$ cat /proc/cpuinfo
...
```

これも非常に長い情報が表示されるのでgrepで絞ります。

- 物理CPUには `physical id` というidが降ってあるのでこれに `uniq` をかけます。
- CPUが1つ認識されている場合は `physical id` も1行だけ出てきます。

```text
$ cat /proc/cpuinfo | grep 'physical id' | uniq
physical id    : 0
```

同じようにgrepを使って様々な情報が見れます。

- 物理コア数

```text
$ cat /proc/cpuinfo | grep 'cpu cores' | uniq
cpu cores    : 6
```

- 論理コア数

```text
$ cat /proc/cpuinfo | grep 'processor' | uniq
processor    : 0
processor    : 1
processor    : 2
processor    : 3
processor    : 4
processor    : 5
processor    : 6
processor    : 7
processor    : 8
processor    : 9
processor    : 10
processor    : 11
```

## GPUのハード情報

- `lspci` で `PCI` 接続しているデバイスの一覧が表示されます。
- 適当にgrepしてGPUを探しましょう。
- 参考: man - lspci
	- [http://manpages.ubuntu.com/manpages/trusty/man8/lspci.8.html](http://manpages.ubuntu.com/manpages/trusty/man8/lspci.8.html)

```text
$ lspci | grep -i nvidia
01:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1)
01:00.1 Audio device: NVIDIA Corporation GP104 High Definition Audio Controller (rev a1)
```

先程はgrepして情報を絞りましたが、左端に出ているidを `-s` オプションに与える事で指定したデバイスだけ見る事ができます。

```text
$ lspci -s 01:00.0 
01:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1)
```

さらに `-v` を付ける事で詳しい情報が表示されます。

```text
$ lspci -s 01:00.0 -v
01:00.0 VGA compatible controller: NVIDIA Corporation GP104 [GeForce GTX 1080] (rev a1) (prog-if 00 [VGA controller])
    Subsystem: Dell GP104 [GeForce GTX 1080]
    Flags: bus master, fast devsel, latency 0, IRQ 136
    Memory at eb000000 (32-bit, non-prefetchable) [size=16M]
    Memory at a0000000 (64-bit, prefetchable) [size=256M]
    Memory at b0000000 (64-bit, prefetchable) [size=32M]
    I/O ports at e000 [size=128]
    Expansion ROM at 000c0000 [disabled] [size=128K]
    Capabilities: <access denied>
    Kernel driver in use: nouveau
    Kernel modules: nvidiafb, nouveau
```

[0](https://qiita.com/sabaku20XX/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fsabaku20XX%2Fitems%2F97db2c0bf7298e3a645c&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fsabaku20XX%2Fitems%2F97db2c0bf7298e3a645c&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[200](https://qiita.com/sabaku20XX/items/97db2c0bf7298e3a645c/likers)

いいねしたユーザー一覧へ移動

158