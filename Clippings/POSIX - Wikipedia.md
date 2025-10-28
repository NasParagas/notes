---
title: "POSIX - Wikipedia"
source: "https://ja.wikipedia.org/wiki/POSIX"
author:
  - "[[ウィキメディアプロジェクトへの貢献者]]"
published: 2004-06-02
created: 2025-08-27
description:
tags:
  - "clippings"
---
**POSIX** （ポジックス [^5] [^6] 、 [英](https://ja.wikipedia.org/wiki/%E8%8B%B1%E8%AA%9E "英語"): Portable Operating System Interface ）は、 [オペレーティングシステム](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0 "オペレーティングシステム") (OS) の標準的な [インタフェース](https://ja.wikipedia.org/wiki/%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9_\(%E6%83%85%E5%A0%B1%E6%8A%80%E8%A1%93\) "インタフェース (情報技術)") および環境を定義する [IEEE](https://ja.wikipedia.org/wiki/IEEE "IEEE") 規格である [^7] 。 [ソースコード](https://ja.wikipedia.org/wiki/%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89 "ソースコード") レベルでの [移植性](https://ja.wikipedia.org/wiki/%E7%A7%BB%E6%A4%8D%E6%80%A7 "移植性") の高い [アプリケーションソフトウェア](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2 "アプリケーションソフトウェア") の開発を容易にすることを目的として、主に [UNIX系](https://ja.wikipedia.org/wiki/UNIX%E7%B3%BB "UNIX系") のOSに関して、各OSが共通して持つべき [コマンドラインインタプリタ](https://ja.wikipedia.org/wiki/%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%83%A9%E3%82%A4%E3%83%B3%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%97%E3%83%AA%E3%82%BF "コマンドラインインタプリタ") （シェル）、共通のユーティリティプログラム、および [アプリケーションプログラミングインタフェース](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9 "アプリケーションプログラミングインタフェース") (API) について定めている [^7] 。POSIX仕様に準拠したソースコードは、POSIX準拠OSであればどれでも動作させることができる。アプリケーション開発者とシステム実装者の両方から利用されることを意図している。

## 概要

### 規格の内容

POSIXのバージョンごとに、定められている項目が異なる。各バージョンで定められている項目を下に示す。

なおPOSIXにはいくつものバージョンがあるので、古い文書などで単に「POSIX」と書いてある場合は、現在「POSIX.1」(IEEE Std 1003.1) と呼ばれているものだけを指している可能性がある。

#### POSIX.1

**POSIX.1** - Core Services (Standard ANSI Cと統合) (IEEE Std 1003.1-1988)

- [Process Creation and Control](https://ja.wikipedia.org/wiki/%E3%83%97%E3%83%AD%E3%82%BB%E3%82%B9%E7%AE%A1%E7%90%86 "プロセス管理")
- [Signals](https://ja.wikipedia.org/wiki/%E3%82%B7%E3%82%B0%E3%83%8A%E3%83%AB_\(Unix\) "シグナル (Unix)")
	- Floating Point Exceptions（ [浮動小数点例外](https://ja.wikipedia.org/w/index.php?title=%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E4%BE%8B%E5%A4%96&action=edit&redlink=1 "浮動小数点例外 (存在しないページ)") ）
	- Segmentation / Memory Violations
	- Illegal Instructions
	- Bus Errors
	- Timers
- File and Directory Operations
- [Pipes](https://ja.wikipedia.org/wiki/%E3%83%91%E3%82%A4%E3%83%97_\(%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF\) "パイプ (コンピュータ)")
- [C Library (Standard C)](https://ja.wikipedia.org/wiki/%E6%A8%99%E6%BA%96C%E3%83%A9%E3%82%A4%E3%83%96%E3%83%A9%E3%83%AA "標準Cライブラリ")
- [I/O](https://ja.wikipedia.org/wiki/%E5%85%A5%E5%87%BA%E5%8A%9B "入出力") Port Interface and Control
- Process Triggers

#### POSIX.1b

**POSIX.1b** - Real-time extensions (IEEE Std 1003.1b-1993) [^1]

- Priority Scheduling
- Real-Time Signals
- Clocks and Timers
- Semaphores
- Message Passing
- Shared Memory
- Asynchronous and Synchronous I/O
- Memory Locking Interface

#### POSIX.1c

**POSIX.1c** - Threads extensions (IEEE Std 1003.1c-1995)　　 [POSIXスレッド](https://ja.wikipedia.org/wiki/POSIX%E3%82%B9%E3%83%AC%E3%83%83%E3%83%89 "POSIXスレッド") も参照

- Thread Creation, Control, and Cleanup
- Thread Scheduling
- Thread Synchronization
- Signal Handling

#### POSIX.2

**POSIX.2** - Shell and Utilities (IEEE Std 1003.2-1992)

- Command Interpreter
- Utility Programs

### 名称の由来

この規格は起源をさかのぼると、もともとはIEEEの規格番号やISO/IEC標準番号などで呼ばれていたものであるが、それが発展していく途中でPOSIXと改名された。最初、この一群の規格は「IEEE 1003」という名でつくられ、ISO/IEC標準での番号は「ISO/IEC 9945」だった。 1988年に「IEEE Std 1003.1-1988」と呼ばれていたころに、並行して「POSIX」という名称でも呼ばれ始めた。POSIXという名前は [リチャード・ストールマン](https://ja.wikipedia.org/wiki/%E3%83%AA%E3%83%81%E3%83%A3%E3%83%BC%E3%83%89%E3%83%BB%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%83%9E%E3%83%B3 "リチャード・ストールマン") がIEEEに提案したものである [^8] 。末尾の「 X 」はUNIX互換OSに「 X 」の字がつく名前が多いことからつけられた。IEEE側のほうも、番号で呼ぶよりもPOSIXという名称で呼んだほうが発音しやすく憶えやすいと気づき、これを採用すると決め、正式名称という位置づけとなった。

## POSIX指向のOS

POSIXの規則を守っていることを英語でPOSIX-compliant（ポジックス・コンプライアント）と言うが、各OSは、どれくらいPOSIXに適合しているかという程度によって、POSIX完全適合のものからPOSIX部分適合のものまで、多段階に分類することができる。

（IEEEはOS開発元から申請があればそのOSがPOSIXに適合しているか審査しており）IEEEでPOSIX認証を受けたOSは、登録されIEEEの公式ウェブサイト内で公表されている [^9] 。認証プログラムのガイドラインが公式サイトに記載されている [^10] 。ただし審査は有料制。 [^2]

### POSIX準拠

以下に挙げるOSのいくつかのバージョンは、POSIXのいずれかのバージョンを満たすとしてIEEEから認証を受けている (POSIX-certified)。
- [AIX](https://ja.wikipedia.org/wiki/IBM_AIX "IBM AIX")[[7]](https://ja.wikipedia.org/wiki/POSIX#cite_note-9)
- [HP-UX](https://ja.wikipedia.org/wiki/HP-UX "HP-UX")[[8]](https://ja.wikipedia.org/wiki/POSIX#cite_note-hp-10)
- [IRIX](https://ja.wikipedia.org/wiki/IRIX "IRIX")[[9]](https://ja.wikipedia.org/wiki/POSIX#cite_note-11)
- [EulerOS](https://ja.wikipedia.org/w/index.php?title=EulerOS&action=edit&redlink=1 "EulerOS (存在しないページ)")[[10]](https://ja.wikipedia.org/wiki/POSIX#cite_note-12)
- [macOS](https://ja.wikipedia.org/wiki/MacOS "MacOS") ([10.5 Leopard](https://ja.wikipedia.org/wiki/Mac_OS_X_Leopard "Mac OS X Leopard")以降)[[11]](https://ja.wikipedia.org/wiki/POSIX#cite_note-13)[[12]](https://ja.wikipedia.org/wiki/POSIX#cite_note-14)
- [Solaris](https://ja.wikipedia.org/wiki/Solaris_\(operating_system\) "Solaris (operating system)")[[13]](https://ja.wikipedia.org/wiki/POSIX#cite_note-15)
- [Tru64](https://ja.wikipedia.org/wiki/Tru64_UNIX "Tru64 UNIX")[[8]](https://ja.wikipedia.org/wiki/POSIX#cite_note-hp-10)
- [UnixWare](https://ja.wikipedia.org/wiki/UnixWare "UnixWare")[[14]](https://ja.wikipedia.org/wiki/POSIX#cite_note-16)
- [QNX Neutrino](https://ja.wikipedia.org/wiki/QNX "QNX")[[15]](https://ja.wikipedia.org/wiki/POSIX#cite_note-17)
- [Inspur K-UX](https://ja.wikipedia.org/w/index.php?title=Inspur_K-UX&action=edit&redlink=1 "Inspur K-UX (存在しないページ)")[[16]](https://ja.wikipedia.org/wiki/POSIX#cite_note-18)
- [INTEGRITY](https://ja.wikipedia.org/w/index.php?title=Integrity_\(operating_system\)&action=edit&redlink=1 "Integrity (operating system) (存在しないページ)")[[17]](https://ja.wikipedia.org/wiki/POSIX#cite_note-19)

### POSIXにおおむね準拠

以下に挙げるものは、IEEEから公式認証を受けてはいないが、おおむねPOSIXに準拠しているものである。
- [Android](https://ja.wikipedia.org/wiki/Android_\(%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0\) "Android (オペレーティングシステム)")[[注釈 3]](https://ja.wikipedia.org/wiki/POSIX#cite_note-20)
- [BeOS](https://ja.wikipedia.org/wiki/BeOS "BeOS") (結果として[Haiku](https://ja.wikipedia.org/wiki/Haiku_\(%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0\) "Haiku (オペレーティングシステム)")も含む)
- [Contiki](https://ja.wikipedia.org/wiki/Contiki "Contiki")
- [Darwin](https://ja.wikipedia.org/wiki/Darwin_\(%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0\) "Darwin (オペレーティングシステム)") ([macOS](https://ja.wikipedia.org/wiki/MacOS "MacOS")および[iOS](https://ja.wikipedia.org/wiki/IOS "IOS")のコア)
- [DragonFly BSD](https://ja.wikipedia.org/wiki/DragonFly_BSD "DragonFly BSD")
- [FreeBSD](https://ja.wikipedia.org/wiki/FreeBSD "FreeBSD")[[18]](https://ja.wikipedia.org/wiki/POSIX#cite_note-FreeBSD-21)
- [illumos](https://ja.wikipedia.org/wiki/Illumos "Illumos")
- [Linux](https://ja.wikipedia.org/wiki/Linux "Linux")[[注釈 4]](https://ja.wikipedia.org/wiki/POSIX#cite_note-22)
- [LynxOS](https://ja.wikipedia.org/wiki/LynxOS "LynxOS")
- [MINIX](https://ja.wikipedia.org/wiki/MINIX "MINIX") (現在[_[いつ?](https://ja.wikipedia.org/wiki/Wikipedia:%E8%A8%80%E8%91%89%E3%82%92%E6%BF%81%E3%81%95%E3%81%AA%E3%81%84 "Wikipedia:言葉を濁さない")_]では[MINIX3](https://ja.wikipedia.org/w/index.php?title=MINIX3&action=edit&redlink=1 "MINIX3 (存在しないページ)"))
- [MPE/iX](https://ja.wikipedia.org/w/index.php?title=HP_Multi-Programming_Executive&action=edit&redlink=1 "HP Multi-Programming Executive (存在しないページ)")[[19]](https://ja.wikipedia.org/wiki/POSIX#cite_note-23)
- [NetBSD](https://ja.wikipedia.org/wiki/NetBSD "NetBSD")
- [Nucleus RTOS](https://ja.wikipedia.org/wiki/Nucleus_RTOS "Nucleus RTOS")
- [NuttX](https://ja.wikipedia.org/w/index.php?title=NuttX&action=edit&redlink=1 "NuttX (存在しないページ)")
- [OpenBSD](https://ja.wikipedia.org/wiki/OpenBSD "OpenBSD")
- [OpenSolaris](https://ja.wikipedia.org/wiki/OpenSolaris "OpenSolaris")[[20]](https://ja.wikipedia.org/wiki/POSIX#cite_note-24)
- [PikeOS](https://ja.wikipedia.org/wiki/PikeOS "PikeOS")
- [Redox](https://ja.wikipedia.org/wiki/Redox_\(%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0\) "Redox (オペレーティングシステム)")
- [RTEMS](https://ja.wikipedia.org/wiki/RTEMS "RTEMS") – POSIX API support designed to IEEE Std. 1003.13-2003 PSE52
- [SkyOS](https://ja.wikipedia.org/wiki/SkyOS "SkyOS")
- [Syllable Desktop](https://ja.wikipedia.org/wiki/Syllable "Syllable")
- [VSTa](https://ja.wikipedia.org/w/index.php?title=VSTa&action=edit&redlink=1 "VSTa (存在しないページ)")
- [VMware ESXi](https://ja.wikipedia.org/wiki/VMware_ESXi "VMware ESXi")
- [Xenix](https://ja.wikipedia.org/wiki/Xenix "Xenix")

[TRON](https://ja.wikipedia.org/wiki/TRON%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88 "TRONプロジェクト") 系の [Nucleus RTOS](https://ja.wikipedia.org/wiki/Nucleus_RTOS "Nucleus RTOS") のように、 [Unix系](https://ja.wikipedia.org/wiki/Unix%E7%B3%BB "Unix系") OS以外でも、すべてではないがPOSIX指向のOSは存在する。

## POSIXと他の規格の関係

[C言語](https://ja.wikipedia.org/wiki/C%E8%A8%80%E8%AA%9E "C言語") の [システムコール](https://ja.wikipedia.org/wiki/%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%82%B3%E3%83%BC%E3%83%AB "システムコール") とライブラリ関数を規定した規格としては、他に [ANSI](https://ja.wikipedia.org/wiki/%E7%B1%B3%E5%9B%BD%E5%9B%BD%E5%AE%B6%E8%A6%8F%E6%A0%BC%E5%8D%94%E4%BC%9A "米国国家規格協会") C/ [ISO](https://ja.wikipedia.org/wiki/%E5%9B%BD%E9%9A%9B%E6%A8%99%E6%BA%96%E5%8C%96%E6%A9%9F%E6%A7%8B "国際標準化機構") CとSUS（ [Single UNIX Specification](https://ja.wikipedia.org/wiki/Single_UNIX_Specification "Single UNIX Specification") 、XPG4の後継）がある。各規格の立場の違いにより、これらが含む関数の種類には差異がある。数学の [包含関係](https://ja.wikipedia.org/wiki/%E5%8C%85%E5%90%AB%E9%96%A2%E4%BF%82 "包含関係") 記号で表すと、ANSI/ISO C ⊂ POSIX.1 ⊂ SUSとなる。

## （参考情報）POSIX非準拠OS

- [Microsoft Windows](https://ja.wikipedia.org/wiki/Microsoft_Windows "Microsoft Windows")

ただし [Windows NT系](https://ja.wikipedia.org/wiki/Windows_NT%E7%B3%BB "Windows NT系") は、 [Windows 7](https://ja.wikipedia.org/wiki/Windows_7 "Windows 7") / [Windows Server 2008 R2](https://ja.wikipedia.org/wiki/Windows_Server_2008_R2 "Windows Server 2008 R2") 世代まではPOSIX 1.0に準拠しているPOSIX向けの [サブシステム](https://ja.wikipedia.org/wiki/%E3%82%B5%E3%83%96%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0 "サブシステム") を搭載していて、POSIXアプリケーションをそのサブシステム上で実行できた [^25] 。 [貿易の技術的障害に関する協定](https://ja.wikipedia.org/wiki/%E8%B2%BF%E6%98%93%E3%81%AE%E6%8A%80%E8%A1%93%E7%9A%84%E9%9A%9C%E5%AE%B3%E3%81%AB%E9%96%A2%E3%81%99%E3%82%8B%E5%8D%94%E5%AE%9A "貿易の技術的障害に関する協定") (WTO/TBT協定) では、非関税障壁として工業製品は国際規格を尊重して仕様を規定することを提唱しているため、 [米国](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%A1%E3%83%AA%E3%82%AB%E5%90%88%E8%A1%86%E5%9B%BD "アメリカ合衆国") 政府機関のコンピュータシステム導入要件 ([連邦情報処理標準](https://ja.wikipedia.org/wiki/%E9%80%A3%E9%82%A6%E6%83%85%E5%A0%B1%E5%87%A6%E7%90%86%E6%A8%99%E6%BA%96 "連邦情報処理標準") 、FIPS) としてPOSIX準拠であることが規定されていたためである [^26] 。 [Windows 2000](https://ja.wikipedia.org/wiki/Microsoft_Windows_2000 "Microsoft Windows 2000") までPOSIXサブシステムを搭載していたが、 [Windows XP](https://ja.wikipedia.org/wiki/Microsoft_Windows_XP "Microsoft Windows XP") からは [Services for UNIX](https://ja.wikipedia.org/wiki/Services_for_UNIX "Services for UNIX") に同梱の [Interix](https://ja.wikipedia.org/wiki/Interix "Interix") サブシステムに役割を譲り、 [Windows Server 2003](https://ja.wikipedia.org/wiki/Windows_Server_2003 "Windows Server 2003") R2や [Windows Vista](https://ja.wikipedia.org/wiki/Windows_Vista "Windows Vista") からは「UNIXベースアプリケーション用サブシステム」(Subsystem for UNIX-based Applications, SUA) となった [^25] 。

しかしマイクロソフトは [Windows 8](https://ja.wikipedia.org/wiki/Windows_8 "Windows 8") および [Windows Server 2012](https://ja.wikipedia.org/wiki/Windows_Server_2012 "Windows Server 2012") よりSUAを非推奨とし、代替手段の一つとして [Cygwin](https://ja.wikipedia.org/wiki/Cygwin "Cygwin") のPOSIX [エミュレーション](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%9F%E3%83%A5%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3 "エミュレーション") モードを紹介するようになった [^27] [^28] 。

[Windows 8.1](https://ja.wikipedia.org/wiki/Windows_8.1 "Windows 8.1") および [Windows Server 2012 R2](https://ja.wikipedia.org/wiki/Windows_Server_2012_R2 "Windows Server 2012 R2") ではSUAは完全に利用できなくなった [^25] 。

その後 [Windows 10](https://ja.wikipedia.org/wiki/Windows_10 "Windows 10") では、Windows 10 version 1607以降でWSL ([Windows Subsystem for Linux](https://ja.wikipedia.org/wiki/Windows_Subsystem_for_Linux "Windows Subsystem for Linux")) を搭載したことにより、POSIX準拠のサブシステムを利用できるようになった。WSLではLinuxアプリケーションを実行することもできる。

  

## 脚注

### 注釈

### 出典

## 関連項目

- [POSIXスレッド](https://ja.wikipedia.org/wiki/POSIX%E3%82%B9%E3%83%AC%E3%83%83%E3%83%89 "POSIXスレッド")
- [POSIX 1003.1b](https://ja.wikipedia.org/wiki/POSIX_1003.1b "POSIX 1003.1b")
- [en:C POSIX library](https://en.wikipedia.org/wiki/C_POSIX_library "en:C POSIX library")
- [Linux Standard Base](https://ja.wikipedia.org/wiki/Linux_Standard_Base "Linux Standard Base")
- [アメリカ国立標準技術研究所](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%A1%E3%83%AA%E3%82%AB%E5%9B%BD%E7%AB%8B%E6%A8%99%E6%BA%96%E6%8A%80%E8%A1%93%E7%A0%94%E7%A9%B6%E6%89%80 "アメリカ国立標準技術研究所")
- [国際化と地域化](https://ja.wikipedia.org/wiki/%E5%9B%BD%E9%9A%9B%E5%8C%96%E3%81%A8%E5%9C%B0%E5%9F%9F%E5%8C%96 "国際化と地域化")
- [共通ロケールデータリポジトリ](https://ja.wikipedia.org/wiki/%E5%85%B1%E9%80%9A%E3%83%AD%E3%82%B1%E3%83%BC%E3%83%AB%E3%83%87%E3%83%BC%E3%82%BF%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA "共通ロケールデータリポジトリ")
- [The Open Group](https://ja.wikipedia.org/wiki/The_Open_Group "The Open Group")
- [Common User Access](https://ja.wikipedia.org/wiki/Common_User_Access "Common User Access")
- [TRONプロジェクト](https://ja.wikipedia.org/wiki/TRON%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88 "TRONプロジェクト")

## 外部リンク

- [POSIX® Certification](http://get.posixcertified.ieee.org/)

<table><tbody><tr><th colspan="2"><div>IEEE標準</div></th></tr></tbody></table>

<table><tbody><tr><th colspan="2"><div>ISO標準</div></th></tr></tbody></table>

[^1]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-4)** 後にlibrt (Realtime Extensions library) となっていった。

[^2]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-8)** その審査料金や認定料や認証後の「POSIX」名の使用料が高額なので [\[1\]](http://get.posixcertified.ieee.org/docs/posix-fee-schedule-1.3.PDF) 、ボランティアらで開発されているオープンなソフトウェアなどは通常そうした余計な費用負担はできず審査を申し込まないので、結果としてPOSIXにほぼ適合していても審査を受けようともしないということが起きている。そのような理由で、実は認証が無いOSでもPOSIXにほぼ適合しているものもある。

[^3]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-20)** ただし、 [Android NDK](https://ja.wikipedia.org/wiki/Android_NDK "Android NDK") を利用する場合に限る。

[^4]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-22)** ほとんどのディストリビューションが該当するが、全てではない。 [Linux Standard Base](https://ja.wikipedia.org/wiki/Linux_Standard_Base "Linux Standard Base") を参照。

[^5]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-1)** [Linux用語事典 \[POSIX（Portable Operating System Interface for UNIX）\]](https://www.atmarkit.co.jp/aig/03linux/posix.html)

[^6]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-2)** “ [POSIX™ 1003.1 Frequently Asked Questions (FAQ Version 1.18)](http://www.opengroup.org/austin/papers/posix_faq.html) ” (2020年6月13日). 2021年5月6日閲覧。 “It is expected to be pronounced pahz-icks, as in positive, not poh-six, or other variations.”

[^7]: ^ [<sup><i><b>a</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-IEEE_Official_3-0) [<sup><i><b>b</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-IEEE_Official_3-1) IEEE公式サイト。 [P1003.1 - Standard for Information Technology--Portable Operating System Interface (POSIX(TM)) Base Specifications, Issue 8](https://standards.ieee.org/project/1003_1.html)

[^8]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-faq_5-0)** “ [POSIX 1003.1 FAQ Version 1.12](http://www.opengroup.org/austin/papers/posix_faq.html) ” (2006年2月2日). 2010年12月29日閲覧。

[^9]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-6)** [POSIX Certification Register](http://get.posixcertified.ieee.org/register.html)

[^10]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-7)** [POSIX Certification Guide](http://get.posixcertified.ieee.org/certification_guide.html#Howto)

[^11]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-9)** “ [IBM](http://www.opengroup.org/openbrand/register/ibm.htm) ”. The Open Group. 2014年1月26日閲覧。

[^12]: ^ [<sup><i><b>a</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-hp_10-0) [<sup><i><b>b</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-hp_10-1) “ [Hewlett-Packard](http://www.opengroup.org/openbrand/register/hp.htm) ”. The Open Group. 2014年1月26日閲覧。

[^13]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-11)** “ [Silicon Graphics, Inc.](http://www.opengroup.org/openbrand/register/sgi.htm)”. The Open Group. 2014年1月26日閲覧。

[^14]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-12)** “ [Huawei Technology Co., Ltd](https://www.opengroup.org/openbrand/register/huawei.htm) ”. The Open Group. 2017年5月26日閲覧。

[^15]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-13)** “ [The Open Brand - Register of Certified Products](http://www.opengroup.org/openbrand/register/brand3555.htm) ”. *Register of Open Branded Products*. The Open Group. 2015年5月20日閲覧。

[^16]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-14)** “ [Apple Inc](http://www.opengroup.org/openbrand/register/apple.htm) ”. *Register of Open Branded Products*. The Open Group. 2015年5月20日閲覧。

[^17]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-15)** “ [Oracle Corporation](http://www.opengroup.org/openbrand/register/sun.htm) ”. The Open Group. 2014年1月26日閲覧。

[^18]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-16)** “ [UnixWare ® 7.1.3 and later](http://www.opengroup.org/openbrand/register/brand2713.htm) ”. The Open Group (2003年5月16日). 2013年6月10日閲覧。

[^19]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-17)** “ [QNX Achieves New POSIX Certification](http://www.qnx.com/news/pr_2862_1.html) ”. QNX (2008年4月8日). 2016年1月16日閲覧。

[^20]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-18)** “ [Inspur Co., Ltd](https://www.opengroup.org/openbrand/register/inspur.htm) ”. The Open Group. 2017年5月26日閲覧。

[^21]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-19)** “ [POSIX Certification Register](http://get.posixcertified.ieee.org/register.html) ”. *get.posixcertified.ieee.org*. 2018年3月9日閲覧。

[^22]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-FreeBSD_21-0)** Schweikhardt, Jens. “ [POSIX utilities](http://people.freebsd.org/~schweikh/posix-utilities.html) ”. FreeBSD. 2019年4月12日閲覧。

[^23]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-23)** Mark Halper (7 November 1994). [“HP 3000 sales catch market by surprise”](https://books.google.com/books?id=-6GcSQAVaHgC&q=posix&pg=PA24). *Computerworld* (IDG Enterprise) **28** (4).

[^24]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-24)** Solter, Nicholas A.; Jelinek, Jerry; Miner, David (21 March 2011) (英語). [*OpenSolaris Bible*](https://books.google.com/books?id=y8qaxiZNvqAC). John Wiley & Sons. [ISBN](https://ja.wikipedia.org/wiki/ISBN "ISBN") [9781118080313](https://ja.wikipedia.org/wiki/%E7%89%B9%E5%88%A5:%E6%96%87%E7%8C%AE%E8%B3%87%E6%96%99/9781118080313 "特別:文献資料/9781118080313")

[^25]: ^ [<sup><i><b>a</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-windows_25-0) [<sup><i><b>b</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-windows_25-1) [<sup><i><b>c</b></i></sup>](https://ja.wikipedia.org/wiki/#cite_ref-windows_25-2) “ [POSIX and UNIX Support in Windows](https://social.technet.microsoft.com/wiki/contents/articles/10224.posix-and-unix-support-in-windows.aspx) ”. 2018年8月10日閲覧。

[^26]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-fips_26-0)** [Federal Information Processing Standard (FIPS) 151-2](https://web.archive.org/web/20140220130516/http://www.itl.nist.gov/fipspubs/fip151-2.htm) - [ウェイバックマシン](https://ja.wikipedia.org/wiki/%E3%82%A6%E3%82%A7%E3%82%A4%E3%83%90%E3%83%83%E3%82%AF%E3%83%9E%E3%82%B7%E3%83%B3 "ウェイバックマシン") （2014年2月20日アーカイブ分）

[^27]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-27)** “ [Features Removed or Deprecated in Windows Server 2012](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831568\(v=ws.11\)#subsystem-for-unix-based-applications) ”. *Microsoft Docs*. 2022年2月17日閲覧。

[^28]: **[^](https://ja.wikipedia.org/wiki/#cite_ref-28)** [第1回　Windows 8製品版の概要：Windows 8レボリューション（1/3 ページ） - ＠IT](https://atmarkit.itmedia.co.jp/ait/articles/1208/23/news133.html)