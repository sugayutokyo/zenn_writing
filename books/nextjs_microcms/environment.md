---
title: "環境構築"
---
## 条件

## next.jsのプロジェクトを作成する

```
$ npx create-next-app myblog -e with-tailwindcss --use-npm
```

## prettierの導入
```
$ npm install --save-dev prettier
```

```
$ touch .prettierrc.yml
```
下記を貼り付ける
```yaml:.prettierrc.yml
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

## VSCodeでprettierが動くようにする
* prettierの拡張機能をインストール
* 「command」 + 「,」でSettingsを開く
* Text Editor -> Formatting の順で選択して、 Format On Save にチェックを入れる
* defaultformatterで検索をかけて、NoneからPrettier - code formatterに変更する
