---
title: "DeepL+Alfred Workflowで翻訳を制する"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nodejs', 'javascript']
published: false
---

## 対象者

## 必要なもの・環境
* DeepL APIのアカウント(Free)
DeepLのアカウントではなく DeepL APIのアカウントが必要となります。
* [Alfred Power Pack](https://www.alfredapp.com/shop/)
有料ですがめちゃくちゃ便利なのでこれを機に導入をお勧めします！
* node v14.13.1以上
ちなみにv14.8.0では正しく動作しませんでした。。。
正しく動作しない場合は上記を満たすようにnodeのバージョンをあげてください！
## Alfredの通知を許可する
今回のワークフローでは翻訳の開始時と終了時にAlfredの通知を送信します。翻訳の状況を確認したい場合ははAlfredの通知をお勧めします。
:::details Alfredの通知を許可する
:::
## DeepL APIのACCESS TOKENを取得する
DeepL APIのアカウントを作成していない場合は下記を参考に作成してください！
不正利用防止のため無料アカウントの作成でもクレジットカード必須となっております。。。
:::details DeepL APIのアカウントを作成する
1. サイトにアクセス
https://www.deepl.com/translator

2. 「無料で体験する」押下
![](https://i.gyazo.com/6c3fd4dca02d4128072af4712d7f5751.png)

3. 「開発者向け」であることを確認して、「無料で登録する」押下
![](https://i.gyazo.com/805e3bd94c96ba69c2c73090ce25046b.png)

4. メールアドレスとパスワードを入力して「続行」押下
![](https://i.gyazo.com/8f78ff9f85a2153d78ce3d3ba3a13942.png)

5. 住所・クレジットカードを入力して「続行」
![](https://i.gyazo.com/135cb1f04ad187d0a1af87a31d666a5c.png)

6. 確認画面で問題なければ「確認」押下
※スクリーンショット撮り忘れてしまいました。おそらく「確認」だと思います。。。

7. 下記画面が表示されれば完了！
![](https://i.gyazo.com/1c313a4b5d3f0d014a2aa3025b56ddbe.png)
:::
1. アカウントを作成後「アカウントを管理する」を押下
![](https://i.gyazo.com/26943a3dcde70bce4d35d09c13360856.png)

:::message
すでにDeepL API Freeをのアカウントを作成している場合は
:::

すでにDeepL API Freeのアカウントを作成していてACCESS TOKENがわからない場合は下記を参考にしてください。
:::details DeepL API Freeのアカウントを作成していてACCESS TOKENがわからない場合
:::
## Alfred Workflowファイルをダウンロード
1. 下記サイトにアクセス
https://github.com/sugayutokyo/alfred_deepl_transrator/releases

2. 最新リリースのalfredworkflowファイルを押下してダウンロード
![](https://i.gyazo.com/f417290937387437a537034df79396cb.png)
## 設定
1. ダウンロードしたalfredworkflowファイルをダブルクリック
![](https://i.gyazo.com/5297a04eba8b83a385578f097656131f.png)

2. 赤枠内にDEEPLのACCESS TOKENを貼り付けて「import」押下
![](https://i.gyazo.com/5b2379b1b28aea65a6b62adacb47ddb7.png)

3. Hotkey(ショートカットキー)を割り当てる
Hotkey は色によって行われる処理が変わります。必要なもののみ割り当ててお使いください！

| 色   | 処理                                   |
|------|--------------------------------------|
| 緑   | 日本語 → 英語に翻訳した後 Clipboard に追加 |
| オレンジ | 英語 → 日本語に翻訳した後 Clipboard に追加 |
| ブルー  | 日本語 → 英語に翻訳した後表示             |
| ピンク  | 英語 → 日本語に翻訳した後表示             |

:::details Hotkey(ショートカットキー)の割り当て方
1. 割り当てたいHotkeyをダブルクリック
※ ピンクを例に説明
![](https://i.gyazo.com/0f8ed607ae15819b6db516d2f9e329ac.png)

2. Hotkeyの項目を選択して割り当てたいコマンドを同時に押下
※画像のように青枠で囲まれていれば選択されています
![](https://i.gyazo.com/d18474cf4e99378433b7c48a2ed2bf5e.png)

3. 入力したコマンドがHotkeyに反映されているのを確認して「Save」押下
![](https://i.gyazo.com/4c9e887271c0016d37f795e8d6fb0940.png)

4. Hotkeyに割り当てたいコマンドが反映されていれば完了！
![](https://i.gyazo.com/c1250feff48832f267bae124144094d5.png)
:::

## 使い方
* 翻訳結果を表示したい
* 翻訳結果をクリップボードにコピーする

## 対象言語の変更の仕方

## この記事のリポジトリ
Node.jsで作成しました。
https://github.com/sugayutokyo/alfred_deepl_transrator

## 終わりに
不具合やこんな機能がほしいなどあれば、コメントなどもらえると嬉しいです！！
直近ではDeepLの有料プランへの対応を考えております。

## 参考
https://blog.chick-p.work/nodejs-aflred-workflow/
https://zenn.dev/ryo_kawamata/articles/alfred-zenn-posts