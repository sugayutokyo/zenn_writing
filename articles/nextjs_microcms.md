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
今回はTailwindCSSを使うためオプション`with-tailwindcss`をつける
ディレクトリ直下にプロジェクトを生成したいので、「.」を指定している（ここはお好みで!）


```shell
$ npm run dev
```
下記画像のようにNext.jsのトップページが表示されればです！
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

