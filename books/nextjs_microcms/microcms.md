---
title: 'microCMS'
---

## microCMS とは

## サービスの作成

1. サービス名、サービス ID を入力して「次へ」押下
   ※サービス ID は変更不可とのこと
   ![](https://storage.googleapis.com/zenn-user-upload/c8fbc8a8d5b7-20211223.png)

2. サービス画像は今回用意しないので「スキップ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/79c9f25192bf-20211223.png)

3. プランは「Hobby」を選択して「完了」押下
   ![](https://storage.googleapis.com/zenn-user-upload/9c44ae03e1c0-20211223.png)

## API の作成

1. API 名、エンドポイントを入力して「次へ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/772286c638d5-20211223.png)

2. 今回は記事を作成するので「リスト形式」を選択して「次へ」押下
   ![](https://storage.googleapis.com/zenn-user-upload/f99261c6cbbb-20211223.png)

3. API スキーマ（インターフェース）は下記画像のように設定して「完了」押下
   ![](https://storage.googleapis.com/zenn-user-upload/a75cc730abe8-20211223.png)

## コンテンツの作成

1. 右上の「追加」押下
   ![](https://storage.googleapis.com/zenn-user-upload/3a02f8ffbfb6-20211223.png)

2. 公開したら下記コマンドで公開された API が取れるかどうか確認する
   API 設定 →API リファレンス →「試してみる」→「取得」

## API を Next.js で実行する

1. 環境変数を用意する

```sh
$ touch .env.local
```

```js:.env.local
API_KEY = 'X-MICROCMS-API-KEYを入力する'
```

2. microCMS の準備
   microcms-js-sdk をインストールする

```sh
$ npm install --save microcms-js-sdk
```

libs/client.js を作成して初期化する

```sh
$ mkdir libs
$ touch ./libs/client.js
```

```js: libs/client.js
// libs/client.js
import { createClient } from 'microcms-js-sdk'

export const client = createClient({
  serviceDomain: 'サービスIDを入力する',
  apiKey: process.env.API_KEY,
})
```

3. 記事一覧を表示する

```jsx:pages/index.js
import Link from 'next/link'
import { client } from '../libs/client'

export default function Home({ articles }) {
  return (
    <div className="p-10 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-3 xl:grid-cols-3 gap-5">
      {articles.map((article) => (
        <div class="rounded overflow-hidden shadow-lg">
          <img
            class="w-full"
            src={article.eye_catch.url}
            alt="Sunset in the mountains"
          />
          <div class="px-6 py-4">
            <Link href={`/article/${article.id}`}>
              <div class="font-bold text-xl mb-2">{article.title}</div>
            </Link>
          </div>
          <div class="px-6 pt-4 pb-2">
            {article.tag && (
              <span class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2 mb-2">
                #{article.tag}
              </span>
            )}
          </div>
        </div>
      ))}
    </div>
  )
}

export const getServerSideProps = async () => {
  const data = await client.get({ endpoint: 'articles' })

  return {
    props: {
      articles: data.contents,
    },
  }
}
```

4. 記事の詳細ページを作る

```sh
$ mkdir ./pages/article
$ touch ./pages/article/\[id\].js
```

```jsx:./pages/article/[id].js
import { client } from '../../libs/client'

export default function Article({ article }) {
  return (
    <div class="mt-6 bg-gray-50">
      <div class=" px-10 py-6 mx-auto">
        <div class="max-w-6xl px-10 py-6 mx-auto bg-gray-50">
          <img
            class="object-cover w-full shadow-sm h-full"
            src={article.eye_catch.url}
          />
          <div class="mt-2">
            <div class="sm:text-3xl md:text-3xl lg:text-3xl xl:text-4xl font-bold text-blue-500  hover:underline">
              {article.title}
            </div>
          </div>
          <div class="flex items-center justify-start mt-4 mb-4">
            <div class="px-2 py-1 font-bold bg-red-400 text-white rounded-lg">
              #{article.tag}
            </div>
          </div>

          <div class="mt-2">
            <div class="text-2xl text-gray-700 mt-4 rounded ">
              {article.body}
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}

export const getServerSideProps = async (ctx) => {
  const id = ctx.params.id
  const data = await client.get({ endpoint: 'articles', contentId: id })

  return {
    props: {
      article: data,
    },
  }
}
```

以下参考にしています。
・microCMS
https://blog.microcms.io/microcms-next-jamstack-blog/
https://blog.microcms.io/what-is-headlesscms/

・TailwindCSS
https://ordinarycoders.com/blog/article/17-tailwindcss-cards
https://tailwindcomponents.com/component/a-single-blog-detail-page
