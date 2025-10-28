---
title: "【C++】Google C++ Style GuideのNaming Rules（命名規則）メモ"
source: "https://yusuke-ujitoko.hatenablog.com/entry/2016/10/20/000000"
author:
  - "[[yusuke_ujitoko]]"
published: 2016-10-20
created: 2025-07-07
description: "Google C++ Style GuideのNaming Rulesメモ 命名規則の種類 一般的に、以下の4種類の命名規則が使われる。 命名規則 説明 PascalCase 先頭大文字、それ以降の単語区切りも大文字 camelCase 先頭小文字、それ以降の単語区切りは大文字 snake_case すべての小文字、単語区切りを\"_\"でつなぐ SCANE_CASE すべて大文字、単語区切りを\"_\"でつなぐ 型の名前(Type Names) 頭文字は大文字 それぞれの単語の頭文字も大文字 アンダースコアは付けない // classes and structs class UrlTable { .…"
tags:
  - "clippings"
---
[Google](http://d.hatena.ne.jp/keyword/Google) [C++](http://d.hatena.ne.jp/keyword/C%2B%2B) Style GuideのNaming Rulesメモ

### 命名規則の種類

一般的に、以下の4種類の [命名規則](http://d.hatena.ne.jp/keyword/%CC%BF%CC%BE%B5%AC%C2%A7) が使われる。

| [命名規則](http://d.hatena.ne.jp/keyword/%CC%BF%CC%BE%B5%AC%C2%A7) | 説明 |
| --- | --- |
| PascalCase | 先頭大文字、それ以降の単語区切りも大文字 |
| camelCase | 先頭小文字、それ以降の単語区切りは大文字 |
| snake\_case | すべての小文字、単語区切りを"\_"でつなぐ |
| SCANE\_CASE | すべて大文字、単語区切りを"\_"でつなぐ |

### 型の名前(Type Names)

- 頭文字は大文字
- それぞれの単語の頭文字も大文字
- アンダースコアは付けない
```cpp
// classes and structs
class UrlTable { ...
class UrlTableTester { ...
struct UrlTableProperties { ...

// typedefs
typedef hash_map<UrlTableProperties *, string> PropertiesMap;

// using aliases
using PropertiesMap = hash_map<UrlTableProperties *, string>;

// enums
enum UrlTableErrors { ...
```

### 変数名(Variable Names)

- すべて小文字
- 単語は"\_"で接続してもよい
```cpp
string table_name;  // OK - uses underscore.
string tablename;   // OK - all lowercase.
```
- クラスのデータメンバは最後に"\_"をつけること．(structの場合はつけない）
```cpp
class TableInfo {
  ...
 private:
  string table_name_;  // OK - underscore at end.
  string tablename_;   // OK.
  static Pool<TableInfo>* pool_;  // OK.
};
```
```cpp
struct UrlTableProperties {
  string name;
  int num_entries;
  static Pool<UrlTableProperties>* pool;
};
```

### 関数名(Function Names)

- 頭文字は大文字
- 単語の頭文字は大文字
- "\_"はつけない
```cpp
AddTableEntry()
DeleteUrl()
OpenFileOrDie()
```