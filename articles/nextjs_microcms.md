---
title: 'Next.js(TypeScript)、TailwindCSS、microCMSでブログを作成しよう！'
emoji: '💻'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'typescript', 'tailwindcss', 'microcms']
published: true
---
## Next.jsとは
React.jsを拡張してサーバー機能やファイルベースルーティングなどが追加されたものです。

詳細は下記URLで確認してください！
https://tech-parrot.com/programming/next-js-react-js-tigai/#3_URLNextjs

## microCMSとは
### そもそもCMSとは
microCMSの説明をする前にCMSとはContents Management Systemの頭文字を並べたもので、コンテンツを管理することができるシステムを指します。

大まかに説明すると「View」「コンテンツ管理画面」「DB」の機能を持ったシステムとなります。
![](https://i.gyazo.com/4f994c1d0a99d666a3f3a7d5b419645e.png)
* 「View」は記事の一覧ページなどを表示する機能。
* 「コンテンツ管理画面」は記事を追加したり、お知らせを追加する機能。
* 「DB」は記事などの情報を保持する機能。

CMSにはいくつか種類がありますが、大まかにはViewの形によって分類することができます。
### View固定型 
この記事を書いているZennも「View固定型」となります。他にはQiitaやnoteなども「View固定型」に含まれます。
Zennを例に説明すると記事をmarkdownで書いて公開すると執筆者はレイアウトのコードを書いていないのにいい感じに表示してくれます。
レイアウトのことを考えずに済むことが「View固定型」の特徴となります。
![](https://i.gyazo.com/8a19719ae6ebc1b5e52d1a83a8be21cb.png)

### View自由型
WordPressが当てはまります。「View自由型」ではViewを自由に変更できるので自分の好みのデザインにすることができます。(自分でCSSなどのデザインを書く必要がありますが...)
レイアウトのことを考える必要があることが「View自由型」の特徴となります。
![](https://i.gyazo.com/651838d0e54561d3e3f4d56d7f0feedc.png)

### microCMSは?
microCMSは上記であげた2つと違い「ヘッドレスCMS」です。ヘッドはViewのことなので「ヘッドレス」=「Viewなし」と言い換えることができます。
![](https://i.gyazo.com/c0c946513f08e95312c12e10b1aff0a1.png)
Viewがないとフロントエンドの選択が自由になります。(React.js、Vue.jsなどのフレームワークで書いてもいいし、ネイティブアプリにしてもいいし)
この記事ではフロントエンドにNext.jsを使ってAPI経由でmicroCMSから記事を取得することでブログサイトを作成します。

## この記事のリポジトリ
https://github.com/sugayutokyo/nextjs_microcms_typescript
うまく行かない処理などありましたら、こちらをご覧ください！

## 環境構築

### next.js のプロジェクトを作成する
```sh
$ npx create-next-app . -e with-tailwindcss
```

* 今回は TailwindCSS を使うためオプション`with-tailwindcss`をつけます
* ディレクトリ直下にプロジェクトを生成したいので、「.」を指定しています（ここはお好みで!）

```sh
$ npm run dev
```

下記画像のように Next.js のトップページが表示されれば OK です！
![](https://i.gyazo.com/e1bfb646892ea04aa37090d1c454e185.png)

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

## Next.js で実装
### Header を作成する

```sh
$ mkdir components
$ touch ./components/Header.tsx
```

```tsx:./components/Header.tsx
import Link from 'next/link'

export default function Header() {
  return (
    <header className="text-gray-600 body-font bg-blue-500">
      <div className="container mx-auto flex flex-wrap p-5 flex-col md:flex-row items-center">
        <Link href={'/'} passHref>
          <a className="flex title-font font-medium items-center text-gray-900 mb-4 md:mb-0">
            <span className="ml-3 text-xl text-white">My Blog</span>
          </a>
        </Link>
        <nav className="md:ml-auto flex flex-wrap items-center text-base justify-center">
          <a className="mr-5 text-white hover:text-gray-900">Profile</a>
        </nav>
      </div>
    </header>
  )
}
```

```diff tsx:./pages/_app.tsx
import '../styles/globals.css';
import type { AppProps } from 'next/app';
+ import Header from '../components/Header';

function MyApp({ Component, pageProps }: AppProps) {
-  return <Component {...pageProps} />;
+  return (
+    <>
+      <Header />
+      <Component {...pageProps} />
+    </>
+  );
}

export default MyApp;
```

下記画像のようにヘッダーが表示されていれば OK です！
![](https://i.gyazo.com/fef51b3691c467f7b897b4eea9f52ff8.png)

### 記事一覧ページを作成する(サンプル)

記事一覧ページにサンプルを一つ表示します
./pages/index.tsx を以下のように変更します(もともと書いてあるコードは全て消してしまって問題ありません!)

```tsx:./pages/index.tsx
export default function Home() {
  return (
    <>
      <h1 className="container mx-auto px-10 pt-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3">
        記事一覧
      </h1>
      <div className="container mx-auto p-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3 gap-5">
        <div className="rounded overflow-hidden shadow-lg">
          <img
            className="w-full"
            src="https://i.gyazo.com/ad13e228541bbaf6952f447cce456dc2.jpg"
            alt="Sunset in the mountains"
          />
          <div className="px-6 py-4">テストタイトル</div>
          <div className="px-6 pt-4 pb-2">
            <span className="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2 mb-2">
              # テストタグ
            </span>
          </div>
        </div>
      </div>
    </>
  )
}
```

トップページが以下の画像のように変われば OK です！
![](https://i.gyazo.com/4e2d381d19194c2dc69bba9b51934bed.png)

## microCMS から記事を作成する
microCMSのアカウントを下記URLから作成してください！
https://microcms.io/
### サービスの作成

1. サービス名、サービス ID を入力して「次へ」押下
   ※サービス ID は変更不可とのこと、後でサービス ID は必要となるのでメモをしておくと良いです
   ![](https://storage.googleapis.com/zenn-user-upload/c8fbc8a8d5b7-20211223.png)

2. サービス画像は今回用意しないので「スキップ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/79c9f25192bf-20211223.png)

3. プランは「Hobby」を選択して「完了」押下
   ![](https://storage.googleapis.com/zenn-user-upload/9c44ae03e1c0-20211223.png)

### API の作成

1. API 名、エンドポイントを入力して「次へ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/772286c638d5-20211223.png)

2. 今回は記事を作成するので「リスト形式」を選択して「次へ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/f99261c6cbbb-20211223.png)

3. API スキーマ（インターフェース）は下記 json ファイルをダウンロードしてファイルインポートする
   [api_schema_test.json](https://firestorage.jp/download/c9d18aa6f2dcd5f9d5cf46ed858d245ac49840b7)
   「こちら」押下
   ![](https://i.gyazo.com/05b8aa71910cb3b8a2200e9dfe33e725.png)
   ダウンロードした json ファイルを選択して下記画像のように表示されることを確認します
   ![](https://i.gyazo.com/72e55a3cb099726e40d36d9806d2e6e6.png)

### コンテンツの作成

1. 右上の「追加」押下してデータを作成する
   ![](https://i.gyazo.com/ad1d8b954dc4963d4081ac1fe933c2d3.png)
   ![](https://i.gyazo.com/d12cd7096326121826fa1ad893cbf9d5.png)

2. 公開したら下記コマンドで公開された API が正しく動作しているか確認する
   「API 設定」→「API リファレンス」→「試してみる」→「取得」
   ![](https://i.gyazo.com/1db03c3aee60b19eee66badcdecaeba9.png)
   ![](https://i.gyazo.com/d06d91db1c2ab4e10b683f45ebaa6912.png)
   ![](https://i.gyazo.com/4193cedf8fd91276d7a01bdee65fa6bc.png)
   ![](https://i.gyazo.com/4291c71527f3ae94e3e4da33705399a0.png)
   ![](https://i.gyazo.com/68ef93bce349079c0916a172e266a9b0.png)

HTTP レスポンスが 200 で返ってきていれば OK です！

### API を Next.js で実行する

1. 環境変数を用意する

```sh
$ touch .env.local
```

microCMS のサイトに戻って「歯車」アイコン押下
![](https://i.gyazo.com/f8a55aa889fc884d752563b4f52058d1.png)

「API キー」押下して copy ボタンを押下
![](https://i.gyazo.com/016ae84b0e136aa96e8dad8dc3efd07e.png)
![](https://i.gyazo.com/f82611b0714694b0411e8a4baef82108.png)

コピーした API_KEY をペーストする

```:.env.local
API_KEY="X-MICROCMS-API-KEYをペーストする"
```

2. microCMS の準備
   microcms-js-sdk をインストールする

```sh
$ npm install --save microcms-js-sdk
```

libs/client.js を作成して初期化する

```sh
$ mkdir libs
$ touch ./libs/client.ts
```

```ts:./libs/client.ts
import { createClient } from 'microcms-js-sdk'
export const client = createClient({
  serviceDomain: 'サービスIDを入力する',
  apiKey: process.env.API_KEY || '',
})
```

:::message
サービス ID を控えていない場合は下記画像のように microCMS の URL から取得することができます。
:::
![](https://i.gyazo.com/e94db593e75f191bb8a1105583ea609e.png)

### 一覧ページを microCMS から取得する

1. ./pages/index.js に getServerSideProps を実装して記事を取得する

```diff tsx:./pages/index.tsx
+ import { client } from '../libs/client';

export default function Home() {
  return (
    ...省略
  );
}

+ export const getServerSideProps = async () => {
+   const data = await client.get({ endpoint: 'articles' });
+
+   return {
+     props: {
+       articles: data.contents,
+     },
+   };
+ };
```

2. 取得した記事の情報をコンポーネントに渡す
   まずは型情報を共通化するために型情報のファイルを作成します
```sh
mkdir ./types
touch ./types/article.ts
```
```ts:./types/article.ts
export type Article = {
  id: string
  createdAt: string
  updatedAt: string
  publishedAt: string
  revisedAt: string
  title: string
  body: string
  eye_catch: {
    url: string
    height: number
    width: number
  }
  tag: string
}
```
型情報をインポートして使います
```diff tsx:./pages/index.tsx
import { client } from '../libs/client';
+ import type { Article } from '../types/article';


+ type Props = {
+   articles: Array<Article>;
+ };

- export default function Home() {
+ export default function Home({ articles }: Props) {
  return (
    ...省略
  );
}

export const getServerSideProps = async () => {
  ...省略
};
```

3. 取得した記事を表示する

```diff tsx:./pages/index.tsx

...省略

export default function Home({ articles }: Props) {
  return (
    <>
      <h1 className="container mx-auto px-10 pt-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3">
        記事一覧
      </h1>
      <div className="container mx-auto p-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3 gap-5">
+       {articles.map(article => (
-         <div className="rounded overflow-hidden shadow-lg">
+         <div className="rounded overflow-hidden shadow-lg" key={article.id}>
            <img
              className="w-full"
-             src="https://i.gyazo.com/ad13e228541bbaf6952f447cce456dc2.jpg"
+             src={article.eye_catch.url}
              alt="Sunset in the mountains"
            />
-           <div className="px-6 py-4">テストタイトル</div>
+           <div className="px-6 py-4">{article.title}</div>
            <div className="px-6 pt-4 pb-2">
+           {article.tag && (
              <span className="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2 mb-2">
-               # テストタグ
+               #{article.tag}
              </span>
+           )}
            </div>
          </div>
+       ))}
      </div>
    </>
  );
}

export const getServerSideProps = async () => {
  ...省略
};
```

下記のように取得したデータに基づいて記事一覧が表示されればOKです!
![](https://i.gyazo.com/63325f715f98f88b5818b5fdc2005c23.png)

### 詳細ページを作成する
```sh
$ mkdir ./pages/article
$ touch ./pages/article/\[id\].jsx
```
下記コードを記述
```tsx:./pages/article/[id].jsx
import { GetServerSideProps } from 'next';
import type { Article } from '../../types/article';
import { client } from '../../libs/client';

type Props = {
  article: Article;
};

export default function Article({ article }: Props) {
  return (
    <div className="bg-gray-50">
      <div className="px-10 py-6 mx-auto">
        <div className="max-w-6xl px-10 py-6 mx-auto bg-gray-50">
          <img
            className="object-cover w-full shadow-sm h-full"
            src={article.eye_catch.url}
          />
          <div className="mt-2">
            <div className="sm:text-3xl md:text-3xl lg:text-3xl xl:text-4xl font-bold text-blue-500">
              {article.title}
            </div>
          </div>
          {article.tag && (
            <div className="flex items-center justify-start mt-4 mb-4">
              <div className="px-2 py-1 font-bold bg-red-400 text-white rounded-lg">
                #{article.tag}
              </div>
            </div>
          )}
          <div className="mt-2">
            <div className="text-2xl text-gray-700 mt-4 rounded ">
              {article.body}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

export const getServerSideProps: GetServerSideProps = async ctx => {
  const id = ctx.params?.id;
  const idExceptArray = id instanceof Array ? id[0] : id;
  const data = await client.get({
    endpoint: 'articles',
    contentId: idExceptArray,
  });

  return {
    props: {
      article: data,
    },
  };
};
```

### 一覧ページから詳細ページに遷移できるようにする
```diff tsx:./pages/article/[id].tsx
import { client } from '../libs/client';
import type { Article } from '../types/article';
+ import Link from 'next/link';

type Props = {
  articles: Array<Article>;
};

export default function Home({ articles }: Props) {
  return (
    <>
      <h1 className="container mx-auto px-10 pt-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3">
        記事一覧
      </h1>
      <div className="container mx-auto p-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3 gap-5">
        {articles.map(article => (
          <div className="rounded overflow-hidden shadow-lg">
            <img
              className="w-full"
              src={article.eye_catch.url}
              alt="Sunset in the mountains"
            />
-           <div className="px-6 py-4">{article.title}</div>
+           <div className="px-6 py-4">
+             <Link href={`/article/${article.id}`} passHref>
+               <a>{article.title}</a>
+             </Link>
+           </div>
            <div className="px-6 pt-4 pb-2">
              ...省略
            </div>
          </div>
        ))}
      </div>
    </>
  );
}

export const getServerSideProps = async () => {
  ...省略
};
```

下記画像のようにタイトルを押下して記事詳細ページに遷移できたら実装完了です！
![](https://i.gyazo.com/e21f53bead2c240353a999bbab1557ec.gif)

お疲れ様でした！！

## 参考

この記事は以下のサイトを参考に作成されています。
* TailwindCSS
https://ordinarycoders.com/blog/article/17-tailwindcss-cards
https://tailwindcomponents.com/component/a-single-blog-detail-page

* microCMS
https://blog.microcms.io/microcms-next-jamstack-blog/
https://blog.microcms.io/what-is-headlesscms/
https://codezine.jp/article/detail/15054
