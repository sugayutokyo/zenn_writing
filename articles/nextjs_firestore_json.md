---
title: "Next.jsでJSONファイルをFirestoreにinsertする"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'firestore', 'json']
published: false
---

## 全体図


## Firestoreにアクセスできるようにする
下記リンクからお手持ちのGoogleアカウントでログインしてください。
https://firebase.google.com/products/firestore/?hl=ja&gclid=EAIaIQobChMIgdOrncS79gIVTUNgCh2yzQ-GEAAYASAAEgLGJ_D_BwE&gclsrc=aw.ds

### Firebaseプロジェクトを作成
作成済みの場合は飛ばしてしまって問題ありません！
1. Firebaseプロジェクトを作成します
![](https://i.gyazo.com/92b8643cc00efb731ee6260fe8c01b8f.png)

2. プロジェクト名を入力し、「Firebaseの規約に同意します」にチェックを入れて「続行」押下
![](https://i.gyazo.com/1d1c353281767e3d25f9bcfd67968073.png)

3. 「Googleアナリティクスを有効にする」はお好みで！不要なら無効で問題ありません！
![](https://i.gyazo.com/48106f5a04e4231a5f95c3728407d9ab.png)
「アナリティクスの地域」→日本に変えて、「Googleアナリティクス利用規約に同意します。」にチェックを入れて、「プロジェクトを作成」押下
![](https://i.gyazo.com/843db09661758bf428f170ebae8fd449.png)

4. プロジェクトが作成し終わったら、「続行」押下
![](https://i.gyazo.com/bdd46cdb81b3af48de9a9b2abbb3b262.png)
![](https://i.gyazo.com/5da89f9551a19c0d29403d384158fc0c.png)

<!-- ### ウェブアプリへの Firebase の追加の準備
1. 画像の赤線のアイコン押下
![](https://i.gyazo.com/a2da7b8f5133a77e6af43a78cf02fb82.png)

2. アプリのニックネームを入力して、「アプリを登録」押下
![](https://i.gyazo.com/2297133831e519eed8ebe40adae5766b.png)

3. 「コンソールに進む」押下 -->

## Firebase Admin SDKの秘密鍵を作成する
1. 画像の赤枠のアイコン（歯車）押下
![](https://i.gyazo.com/3b14c5008177d0556d568b6137de6f30.png)

2. 「プロジェクトの設定」押下
![](https://i.gyazo.com/6559c512fd7502c93c3882e37bd04a44.png)

3. 「サービスアカウント」タブ押下→「サービスアカウントを作成」押下
スクショ撮り忘れました。。。

4. Node.jsが選択されていることを確認して、「新しい秘密鍵の生成」押下
![](https://i.gyazo.com/242b8660566e8c9e5e901826c2318311.png)

5. 「キーを生成」押下
![](https://i.gyazo.com/c0c79ced2d1fd9fc3e5e437b2e0d3003.png)

6. jsonファイルがダウンロードされたら完了(後で使用するため保管してください)

## Next.jsの環境を作成
```sh
## Next.jsのプロジェクトを作成
$ npx create-next-app . -e with-tailwindcss

## npmモジュールを生成
$ npm i

## ローカルサーバー起動
$ npm run dev

```

## Firestoreにデータをinsertできるようにする

## jsonを選択できるようにする

## 選択したjsonの内容をFirestoreにinsertする

## 参考
Firestore
