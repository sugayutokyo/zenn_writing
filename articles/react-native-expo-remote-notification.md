---
title: "Next.jsからReact Native Expoにリモート通知を送信してみた"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['reactnative', 'nextjs']
published: true
---

## 対象者
* NativeアプリをExpoで開発しようとしててPush通知について調べている人
* ExpoでPush通知を導入したい人

## 準備
React Native Expoの環境構築

## ローカル通知とリモート通知の違い
NativeアプリのPush通知には「ローカル通知」と「リモート通知」の2種類があります。
この二つの違いは「**外部サーバが必要かどうか**」となります。

### ローカル通知の例
身近でローカル通知といえば下記画像のような「許可を求める通知」などが挙げられます。
Push通知を外部サーバにリクエストするのではなく端末内のみの処理でPush通知を表示しています。
![](https://i.gyazo.com/35f6b648e141ef5cb30577438f86fac7.jpg =250x)

### リモート通知の例
身近でリモート通知といえば「LINEのメッセージが届いた際に表示されるPush通知」などが挙げられます。
流れは以下画像のようになります。
![](https://i.gyazo.com/983ae61cd57c7961c0e1d66e927a9702.jpg)

① LINEサーバでメッセージを受け取る 

② 外部サーバ(AppleやGoogleが提供するサーバ)に通知情報を送る
iOSの場合はAppleが提供する通知サーバ、Androidの場合はGoogleが提供する通知サーバに対してLINEサーバから通知のリクエストを送ります。
リクエストには「どの端末か特定できるID」「通知に表示させたい文字」などが含まれます。

③ 外部サーバから端末に通知を送信する

④ 端末で通知を受け取る

上記の画像は簡潔にまとめたものなので、もっと詳しく知りたい方は以下のサイトを見てみてください！
https://ascii.jp/elem/000/001/235/1235153/

### React Native Expoの場合のリモート通知の仕組み
React Native Expoの場合はExpoの通知サーバにリクエストを送るだけで勝手に各通知サーバにリクエストを送ってくれます！
便利すぎる。。。
![](https://i.gyazo.com/d92ce474cba7adb673d32ecc0cf23596.jpg)
この記事ではNext.jsで作成したWebアプリからExpoの通知サーバにリクエストを送って、リモート通知を実現します！

## Expo Push Tokenを取得する
Expoの通知サーバにリクエストを送る際にExpo Push Tokenが必要となります。
1. 下記サイトの「５．実装②Push通知許可と通知用トークンの取得」のApp.jsの記述を利用してPush通知用のトークンを取得する
[expoによるpush通知（ios）の実装](https://blog.sbworks.jp/2021/04/01/reactnative%EF%BC%86expo%E3%81%AB%E3%82%88%E3%82%8Bpush%E9%80%9A%E7%9F%A5%EF%BC%88ios%EF%BC%89%E3%81%AE%E5%AE%9F%E8%A3%85/)
:::message
* Expo Push Tokenは実機のみ取得できます。**シミュレータだと取得できない**ことに注意してください！
* 上記の記事ではiOSのみ言及されていますが、同じコードでAndroidのExpo Push Tokenを取得することも確認できました！！
:::
* 「Push用のトークンを取得」押下
![](https://i.gyazo.com/0c99b1a0358958d131b8c90dcbcd1638.png =250x)
* 表示されている「ExponentPushToken[~]」を控える
※ターミナルに出力されているはずなのでそちらをコピーする 
![](https://i.gyazo.com/c548c5d3f1472f76acab8bf696c1cacb.png =250x)

## Next.jsで通知を送信してみる
### 環境構築(next.js のプロジェクトを作成する)
$ npx create-next-app . -e with-tailwindcss

今回は TailwindCSS を使うためオプションwith-tailwindcssをつけます
ディレクトリ直下にプロジェクトを生成したいので、「.」を指定しています（ここはお好みで!）
$ npm run dev

下記画像のように Next.js のトップページが表示されれば OK です！
![](https://i.gyazo.com/e850f31b903b3e2dc143d0929b4cf8d0.png)

本記事では prettier を導入しています。導入したい方は以下の YAML ファイルを参考に設定してください！

```yml:.prettierrc.yml
printWidth: 80
tabWidth: 2
semi: true
singleQuote: true
quoteProps: as-needed
jsxSingleQuote: false
trailingComma: all
bracketSpacing: true
jsxBracketSameLine: true
arrowParens: avoid
endOfLine: lf
```

### Push通知を送信するボタンを作成する
./pages/index.tsx を以下のように変更します(もともと書いてあるコードは全て消してしまって問題ありません!)
```tsx:./pages/index.tsx
export default function Home() {
  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="rounded bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
        onClick={() => alert()}>
        テストプッシュ通知
      </button>
    </div>
  );
}
```
下記画像のように表示されればOKです！
![](https://i.gyazo.com/3e5d6296be25efa2401d79da92366911.png)

### 環境変数を用意する
今回はテストなためExpo Tokenを環境変数に入れて使用することにします。
実際はユーザーに対応したExpo TokenをDBから取得するような流れになるかと思います...
```sh
$ touch .env.local
```

```:.env.local
DEVICE_TOKEN=Expo Tokenをペーストする
```

### リモート通知を送信する
1. node-fetchライブラリを導入する
fetchを使って通知のリクエストを行うので対応するライブラリを導入します。
```sh
$ npm i node-fetch
```

2. リモート通知を送信するAPIを作成する
下記のようにAPIを作成してください！
```sh
$ touch ./pages/api/sendPushApi.ts
```
```ts:./pages/api/sendPushApi.ts
import fetch from 'node-fetch';

export default (req: any, res: { statusCode: number; }) => {
  res.statusCode = 200
  if (process.env.DEVICE_TOKEN) {
    const pushMessage = {
      to: process.env.DEVICE_TOKEN,
      sound: 'default',
      title: 'いつもご利用いただきありがとうございます。',
      body: '今後もよろしくお願いいたします。',
    };
    fetch('https://exp.host/--/api/v2/push/send', {
      method: 'POST',
      headers: {
        Accept: 'application/json',
        'Accept-encoding': 'gzip, deflate',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(pushMessage),
    });
  } else {
    console.log('.env.localにtokenが設定されていません。');
  }
}
```

### ボタン押下時にプッシュ通知が送信されるように修正する
あとは作成したAPIをボタン押下時に走らせれば完了です！
```diff tsx:./pages/index.tsx
export default function Home() {
+  const sendPush = () => {
+    fetch('/api/sendPushApi');
+  };
  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="rounded bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
-        onClick={() => alert()}>
+        onClick={() => sendPush()}>
        テストプッシュ通知
      </button>
    </div>
  );
}
```

### 動作確認
1. 「テストプッシュ通知」押下
![](https://i.gyazo.com/554ad753268e734c941d97ba36554b62.png)

2. 下記画像のようにExpo Tokenを取得した端末に通知が届いていれば無事実装完了です！
![](https://i.gyazo.com/6cdca7e6e7c7015f0452c0ec06f0b382.png =250x)

お疲れ様でした！

## 参考
https://qiita.com/merarli/items/9d427aee9065950a45f7

https://qiita.com/mildsummer/items/9dbfac3bf2ef857ca74f

https://zenn.dev/terrierscript/books/2020-09-next-js/viewer/1-5