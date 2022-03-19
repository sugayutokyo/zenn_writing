---
title: "Next.jsでJSONファイルをFirestoreにinsertする"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'firestore', 'json', 'typescript']
published: false
---

## 今回やりたいこと

## 全体図


## Firebaseにログインする
下記リンクからお手持ちのGoogleアカウントでログインしてください。
https://firebase.google.com/products/firestore/?hl=ja&gclid=EAIaIQobChMIgdOrncS79gIVTUNgCh2yzQ-GEAAYASAAEgLGJ_D_BwE&gclsrc=aw.ds

## Firebaseプロジェクトを作成
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
![](https://i.gyazo.com/6bc3a046d45477596e7b3a6433babc07.png)


4. Node.jsが選択されていることを確認して、「新しい秘密鍵の生成」押下
![](https://i.gyazo.com/242b8660566e8c9e5e901826c2318311.png)

5. 「キーを生成」押下
![](https://i.gyazo.com/c0c79ced2d1fd9fc3e5e437b2e0d3003.png)

6. jsonファイルがダウンロードされたら完了(後で使用するため保管してください)

## collectionを作成する

## Next.jsの環境を作成
```sh
## Next.jsのプロジェクトを作成
$ npx create-next-app . -e with-tailwindcss

## npmモジュールを生成
$ npm i

## ローカルサーバー起動
$ npm run dev
```
下記画像のように表示されれば完了
![](https://i.gyazo.com/b22615b1318904c0df5829b17f5aa86c.png)

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

## Firebase Admin SDKの秘密鍵をNext.jsに反映する
1. 「Firebase Admin SDKの秘密鍵を作成する」で作成したjsonファイルをリネームする
本記事では「firebase-test-serviceAccount.json」とリネームしています。
※ リネームはしなくても大丈夫です！分かりやすくするためにリネームしています。
![](https://i.gyazo.com/3970de1b92813fd110466c5ec442929a.png)

2. .gitignoreに追加してgit上の管理から外す
```sh:.gitignore
firebase-test-serviceAccount.json
```

3. firebase-test-serviceAccount.jsonファイルをNext.jsのルートにコピーする
gitignoreに追加しているためgitの管理から外れているはずです。
![](https://i.gyazo.com/1f8a12c4fe832d64753e34ca92418629.png)


## Firestoreの操作をするAPIを作成する
1. Firebase Admin SDKをインストールする
```sh
$ npm i firebase-admin
```
1. apiのファイルを作成する
```sh
$ touch pages/api/user.ts
```

2. 
```ts:pages/api/user.ts
import type { NextApiRequest, NextApiResponse } from 'next';
const { cert } = require('firebase-admin/app');
const { getFirestore } = require('firebase-admin/firestore');
const serviceAccount = require('firebase-test-serviceAccount.json'); // 秘密鍵を取得
const admin = require('firebase-admin');

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse,
) {
  const COLLECTION_NAME = 'users';
  //　初期化する
  if (admin.apps.length === 0) {
    admin.initializeApp({
      credential: cert(serviceAccount),
    });
  }
  const db = getFirestore();
  res.status(200);
}
```




## トップページにapiを実行するボタンを作成する


## Firestoreにデータをinsertできるようにする

## jsonを選択できるようにする

## 選択したjsonの内容をFirestoreにinsertする

## 参考
Firestore
