---
title: 'Next.js(TypeScript)とTailwindCSSとmicroCMSで自作ブログを作成しよう！'
emoji: '💻'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'typescript', 'tailwindcss', 'microcms']
published: false
---



# 環境構築

## next.js のプロジェクトを作成する

```shell
$ npx create-next-app . -e with-tailwindcss
```

今回は TailwindCSS を使うためオプション`with-tailwindcss`をつけます
ディレクトリ直下にプロジェクトを生成したいので、「.」を指定しています（ここはお好みで!）

```shell
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

# Next.js で実装

## Header を作成する

```shell
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

```diff tsx:./pages/_app.jsx
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

## 記事一覧ページを作成する(サンプル)

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
  );
}
```

トップページが以下の画像のように変われば OK です！
![](https://i.gyazo.com/4e2d381d19194c2dc69bba9b51934bed.png)

## microCMS から記事を作成する

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
ダウンロードしたjsonファイルを選択して下記画像のように表示されることを確認します
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

HTTPレスポンスが200で返ってきていればOKです！
