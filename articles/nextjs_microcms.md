---
title: "Next.js(TypeScript)とTailwindCSSとmicroCMSで自作ブログを作成しよう！"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'typescript', 'tailwindcss', 'microcms']
published: false
---

# 環境構築

## next.jsのプロジェクトを作成する

```shell
$ npx create-next-app . -e with-tailwindcss
```
今回はTailwindCSSを使うためオプション`with-tailwindcss`をつけます
ディレクトリ直下にプロジェクトを生成したいので、「.」を指定しています（ここはお好みで!）


```shell
$ npm run dev
```
下記画像のようにNext.jsのトップページが表示されればOKです！
![](https://i.gyazo.com/e1bfb646892ea04aa37090d1c454e185.png)

本記事ではprettierを導入しています。導入したい方は以下のYAMLファイルを参考に設定してください！
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

# Next.jsで実装
## Headerを作成する
```shell
$ mkdir components
$ touch ./components/Header.tsx
```
```tsx:./components/Header.tsx
import Link from 'next/link';

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
  );
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

下記画像のようにヘッダーが表示されていればOKです！
![](https://i.gyazo.com/fef51b3691c467f7b897b4eea9f52ff8.png)

## 記事一覧ページを作成する(サンプル)
記事一覧ページにサンプルを一つ表示します。
./pages/index.tsxを以下のように変更します。(もともと書いてあるコードは全て消してしまって問題ありません!)
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

トップページが以下の画像のように変わればOKです！
![](https://i.gyazo.com/4e2d381d19194c2dc69bba9b51934bed.png)
