---
title: "Recoil"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
## useContext


## Recoil
### Recoilのインストール
```sh
$ npm install recoil
```

エラーメッセージ
![](https://i.gyazo.com/833438d6c1fb6dd44d2f8b8befd71215.png)
```
next-dev.js?3515:32 Warning: Text content did not match. Server: "0" Client: "4"
    at p
    at Profile (webpack-internal:///./pages/profile.tsx:20:71)
    at RecoilRoot_INTERNAL (webpack-internal:///./node_modules/recoil/es/recoil.js:3924:3)
    at RecoilRoot (webpack-internal:///./node_modules/recoil/es/recoil.js:4080:5)
    at MyApp (webpack-internal:///./pages/_app.tsx:41:27)
    at StyleRegistry (webpack-internal:///./node_modules/styled-jsx/dist/stylesheet-registry.js:231:34)
    at ErrorBoundary (webpack-internal:///./node_modules/@next/react-dev-overlay/lib/internal/ErrorBoundary.js:26:47)
    at ReactDevOverlay (webpack-internal:///./node_modules/@next/react-dev-overlay/lib/internal/ReactDevOverlay.js:90:23)
    at Container (webpack-internal:///./node_modules/next/dist/client/index.js:331:9)
    at AppContainer (webpack-internal:///./node_modules/next/dist/client/index.js:770:26)
    at Root (webpack-internal:///./node_modules/next/dist/client/index.js:891:27) 

See more info here: https://nextjs.org/docs/messages/react-hydration-error
```

参考
https://qiita.com/yoshinoritera55/items/57a14fc08641631452bc
https://recoiljs.org/docs/introduction/getting-started/
https://qiita.com/zaburo/items/225d0731faeaa6966ea9

Recoilの永続化
https://maasaablog.com/development/react/3226/#toc12
