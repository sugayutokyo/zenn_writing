---
title: "Next.jsでFirestoreのCRUD操作をやってみる"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'firestore', 'typescript']
published: true
---

## 本記事でやりたいこと
Next.jsでFirestoreのCRUD操作をする

## 本記事のリポジトリ
https://github.com/sugayutokyo/nextjs-firestore-json
うまく行かない処理などありましたら、こちらをご覧ください！

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

## データベースを作成
1. 構築→「Firestore Database」押下
![](https://i.gyazo.com/84981ecf5da3f36925e88302ce5ceb6a.png)

2. 「データベースの作成」押下
![](https://i.gyazo.com/569ab6f2325d0e7856b933e16c2b1a50.png)

3. 「本番環境モード」が選択されていることを確認して、「次へ」押下
![](https://i.gyazo.com/afebdf1477a543484a5e9a04d20ea761.png)

4. 「Cloud Firestoreのロケーション」で「asia-northeast1」を選択して「有効にする」押下
![](https://i.gyazo.com/3c484054e5806be5ba5d0f620efc07de.png)

ロケーションに関して他のものを選択したい場合は下記を参考にしてください
https://firebase.google.com/docs/firestore/locations?hl=ja

5. 下記画像のように表示されれば完了
![](https://i.gyazo.com/736808674889dcdb8a11a156e258d6fe.png)

:::message
下記画像のようにコレクションを読み込めない場合は「再読み込み」を押下してください
![](https://i.gyazo.com/959dc76baa52e2518f3245e996c0c628.png)
:::

### コレクションを作成
まだコレクションが存在しないため作成します。
1. 「コレクションを開始」押下
![](https://i.gyazo.com/5ab7df535f7e3be109f9cb17c58b8634.png)

2. 「コレクションID」にusersと入力し「次へ」押下
![](https://i.gyazo.com/3c060002f552b8ff7961826a08ad9420.png)

3. 「自動ID」押下でドキュメントIDを自動で割り当てる
![](https://i.gyazo.com/1be4dcbb7e9980c52076edb0a78965a2.png)

4. 「保存」押下
![](https://i.gyazo.com/90d4ca7eb3f5f83df324d4da9ab415c5.png)

5. 下記画像のようにコレクションが作成されれば完了
![](https://i.gyazo.com/9e926a49ba82e3a7f0bf432660497eb2.png)

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

## 実際にCRUD操作を行なってみる
### 前準備
1. Firebase Admin SDK、axiosをインストールする
```sh
$ npm i firebase-admin
$ npm i axios
```
2. apiのファイルを作成する
```sh
$ touch pages/api/user.ts
```

### insert
1. Firestoreにデータをinsertするコードをuser.tsに記述
```ts:pages/api/user.ts
import type { NextApiRequest, NextApiResponse } from 'next';
const { cert } = require('firebase-admin/app');
const { getFirestore } = require('firebase-admin/firestore');
const serviceAccount = require('../../firebase-test-serviceAccount.json'); // 秘密鍵を取得
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

  if (req.method === 'POST') {
    const docRef = db.collection(COLLECTION_NAME).doc();
    const insertData = {
      datano: '1',
      name: 'Symfo',
      email: 'symfo@example.com',
    };
    docRef.set(insertData);
  }
  res.status(200);
}
```
2.  トップページにinsertを実行するボタンを作成
index.tsxを下記コードで全て書き換える
```tsx:pages/index.tsx
import type { NextPage } from 'next';
import axios from 'axios';

const Home: NextPage = () => {
  const insertUser = async () => {
    await axios.post('/api/user');
  };

  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="mt-4 w-60 rounded-full bg-green-500 py-2 px-4 font-bold text-white hover:bg-green-700"
        onClick={() => insertUser()}>
        Insert User
      </button>
    </div>
  );
};

export default Home;
```

3. トップ画面の「Insert User」押下でFirestoreにデータができていれば完了
![](https://i.gyazo.com/ab39337705e212394de7cbdb91700f48.png)

![](https://i.gyazo.com/0a9b6e889c499cdb7c2830858b0e16b6.png)

### update
1. 更新対象のdocument idを控える（赤枠内の文字列）
![](https://i.gyazo.com/dae8126d7caf0362d146d6eed79a2291.png)


2. データをupdateするコードをuser.tsに記述(1.で控えたdocument idをtargetDocに設定する)
```diff ts:pages/api/user.ts
import type { NextApiRequest, NextApiResponse } from 'next';
const { cert } = require('firebase-admin/app');
const { getFirestore } = require('firebase-admin/firestore');
const serviceAccount = require('../../firebase-test-serviceAccount.json'); // 秘密鍵を取得
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
+ const targetDoc = 'uxngvlaMwDc0Ye2WnQhN'; //書き換える
  if (req.method === 'POST') {
    const docRef = db.collection(COLLECTION_NAME).doc();
    const insertData = {
      datano: '1',
      name: 'Symfo',
      email: 'symfo@example.com',
    };
    docRef.set(insertData);
+ } else if (req.method === 'PATCH') {
+   const docRef = db.collection(COLLECTION_NAME).doc(targetDoc);
+   const updateData = {
+     datano: '1',
+     name: 'updateSynfo',
+     email: 'updateSynfo@example.com',
+   };
+   docRef.set(updateData);
  }
  res.status(200);
}

```

3. トップページにupdateを実行するボタンを作成
```diff tsx:pages/index.tsx
import type { NextPage } from 'next';
import axios from 'axios';

const Home: NextPage = () => {
  const insertUser = async () => {
    await axios.post('/api/user');
  };
+ const updateUser = async () => {
+   await axios.patch('/api/user');
+ };

  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="mt-4 w-60 rounded-full bg-green-500 py-2 px-4 font-bold text-white hover:bg-green-700"
        onClick={() => insertUser()}>
        Insert User
      </button>
+    <button
+      className="mt-4 w-60 rounded-full bg-yellow-500 py-2 px-4 font-bold text-white hover:bg-yellow-700"
+      onClick={() => updateUser()}>
+      Update User
+    </button>
    </div>
  );
};

export default Home;
```

4. トップ画面の「Update User」押下でFirestoreのデータが更新されていれば完了
![](https://i.gyazo.com/79283a7cc6969e9782b919e576a90d58.png)
![](https://i.gyazo.com/744d1d6d29e18a189fc523f5428ed7e4.png)

### get
1. データをgetするコードをuser.tsに記述
```diff ts:pages/api/user.ts
...
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

  const targetDoc = 'uxngvlaMwDc0Ye2WnQhN'; //書き換える
  if (req.method === 'POST') {
    ...
  } else if (req.method === 'PATCH') {
    const docRef = db.collection(COLLECTION_NAME).doc(targetDoc);
    const updateData = {
      datano: '1',
      name: 'updateSynfo',
      email: 'updateSynfo@example.com',
    };
    docRef.set(updateData);
+ } else if (req.method === 'GET') {
+   const doc = await db.collection(COLLECTION_NAME).doc(targetDoc).get();
+   console.log(doc);
  }
  res.status(200);
}
```

2. トップページにgetを実行するボタンを作成
```diff tsx:pages/index.tsx
import type { NextPage } from 'next';
import axios from 'axios';

const Home: NextPage = () => {
  const insertUser = async () => {
    await axios.post('/api/user');
  };
  const updateUser = async () => {
    await axios.patch('/api/user');
  };
+ const getUser = async () => {
+   await axios.get('/api/user');
+ };

  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="mt-4 w-60 rounded-full bg-green-500 py-2 px-4 font-bold text-white hover:bg-green-700"
        onClick={() => insertUser()}>
        Insert User
      </button>
      <button
        className="mt-4 w-60 rounded-full bg-yellow-500 py-2 px-4 font-bold text-white hover:bg-yellow-700"
        onClick={() => updateUser()}>
        Update User
      </button>
+    <button
+      className="mt-4 w-60 rounded-full bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
+      onClick={() => getUser()}>
+      Get User
+    </button>
    </div>
  );
};

export default Home;
```

3. トップ画面の「Get User」押下でFirestoreのデータがターミナルに表示されていれば完了
![](https://i.gyazo.com/540d4842f2f98427b00c0820ac8fcb00.png)
下記のように_fieldsProtoにFirestoreに格納されている内容がターミナルに表示されていればOK
```sh
QueryDocumentSnapshot {
  _fieldsProto: {
    name: { stringValue: 'updateSynfo', valueType: 'stringValue' },
    email: {
      stringValue: 'updateSynfo@example.com',
      valueType: 'stringValue'
    },
    datano: { stringValue: '1', valueType: 'stringValue' }
  },
  _ref: DocumentReference {
    _firestore: Firestore {
      _settings: [Object],
      _settingsFrozen: true,
      _serializer: [Serializer],
      _projectId: 'next-js-firestore-json',
      registeredListenersCount: 0,
      bulkWritersCount: 0,
      _backoffSettings: [Object],
      _clientPool: [ClientPool]
    },
    _path: ResourcePath { segments: [Array] },
    _converter: {
      toFirestore: [Function: toFirestore],
      fromFirestore: [Function: fromFirestore]
    }
  },
  _serializer: Serializer {
    createReference: [Function (anonymous)],
    createInteger: [Function (anonymous)],
    allowUndefined: false
  },
  _readTime: Timestamp { _seconds: 1647837063, _nanoseconds: 504465000 },
  _createTime: Timestamp { _seconds: 1647831093, _nanoseconds: 420866000 },
  _updateTime: Timestamp { _seconds: 1647836054, _nanoseconds: 546874000 }
}
```

### delete
1. データをdeleteするコードをuser.tsに記述
```diff ts:pages/api/user.ts
...

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse,
) {
  ...

  const targetDoc = 'uxngvlaMwDc0Ye2WnQhN'; //書き換える
  if (req.method === 'POST') {
    ...
  } else if (req.method === 'PATCH') {
    ...
  } else if (req.method === 'GET') {
    ...
+ } else if (req.method === 'DELETE') {
+   const doc = await db.collection(COLLECTION_NAME).doc(targetDoc).delete();
  }
  res.status(200);
}
```

2. トップページにdeleteを実行するボタンを作成
```diff tsx:pages/index.tsx
import type { NextPage } from 'next';
import axios from 'axios';

const Home: NextPage = () => {
  const insertUser = async () => {
    await axios.post('/api/user');
  };
  const updateUser = async () => {
    await axios.patch('/api/user');
  };
  const getUser = async () => {
    await axios.get('/api/user');
  };
+ const deleteUser = async () => {
+   await axios.delete('/api/user');
+ };

  return (
    <div className="flex min-h-screen flex-col items-center justify-center py-2">
      <button
        className="mt-4 w-60 rounded-full bg-green-500 py-2 px-4 font-bold text-white hover:bg-green-700"
        onClick={() => insertUser()}>
        Insert User
      </button>
      <button
        className="mt-4 w-60 rounded-full bg-yellow-500 py-2 px-4 font-bold text-white hover:bg-yellow-700"
        onClick={() => updateUser()}>
        Update User
      </button>
      <button
        className="mt-4 w-60 rounded-full bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
        onClick={() => getUser()}>
        Get User
      </button>
+     <button
+       className="mt-4 w-60 rounded-full bg-red-500 py-2 px-4 font-bold text-white hover:bg-red-700"
+       onClick={() => deleteUser()}>
+       Delete User
+     </button>
    </div>
  );
};

export default Home;
```

3. トップ画面の「Delete User」押下でFirestoreのデータが削除されていればOK
![](https://i.gyazo.com/fc0b22a627f89270b714fb431889ca9b.png)
![](https://i.gyazo.com/ff072acbdaef51561b301801f38c54f5.png)


お疲れ様でした！

## 参考
* Firebase
https://zenn.dev/nash/articles/131441b8999209
https://zenn.dev/nash/articles/6e18bd94eca63e
https://off.tokyo/blog/next-js-node-firebase-firestore/

* Next.js
https://nishimura.club/rest-api-implementation-with-nextjs-api-routes