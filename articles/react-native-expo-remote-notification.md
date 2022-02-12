---
title: "Next.jsからReact native Expoにリモート通知を送信してみた"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['reactnative', 'nextjs']
published: false
---

## Expo Tokenを取得する
1. 下記サイトの「５．実装②Push通知許可と通知用トークンの取得」のApp.jsの記述を利用してPush通知用のトークンを取得する
[expoによるpush通知（ios）の実装](https://blog.sbworks.jp/2021/04/01/reactnative%EF%BC%86expo%E3%81%AB%E3%82%88%E3%82%8Bpush%E9%80%9A%E7%9F%A5%EF%BC%88ios%EF%BC%89%E3%81%AE%E5%AE%9F%E8%A3%85/)
:::message
* Expo Tokenは実機のみ取得できます。**シミュレータだと取得できない**ことに注意してください！
* 上記の記事ではiOSのみ言及されていますが、同じコードでAndroidのExpo Tokenを取得することも確認できました！！
:::
* 「Push用のトークンを取得」押下
![](https://i.gyazo.com/0c99b1a0358958d131b8c90dcbcd1638.png)
* 表示されている「ExponentPushToken[~]」を控える
※ターミナルに出力されているはずなのでそちらをコピーする 
![](https://i.gyazo.com/c548c5d3f1472f76acab8bf696c1cacb.png)

## Next.jsで通知を送信してみる


## 参考
https://qiita.com/merarli/items/9d427aee9065950a45f7

https://qiita.com/mildsummer/items/9dbfac3bf2ef857ca74f

https://zenn.dev/terrierscript/books/2020-09-next-js/viewer/1-5