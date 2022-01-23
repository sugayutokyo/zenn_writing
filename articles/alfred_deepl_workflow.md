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
* Alfred Power Pack
有料ですがめちゃくちゃ便利なのでこれを機に導入をお勧めします！
* node v14.13.1以上
ちなみにv14.8.0では正しく動作しませんでした。。。
正しく動作しない場合は上記を満たすようにnodeのバージョンをあげてください！
## Alfredの通知を許可する
今回のワークフローでは翻訳の開始時と終了時にAlfredの通知を送信します。翻訳の状況を確認したい場合ははAlfredの通知をお勧めします。
:::details Alfredの通知を許可する
:::
## DeepL APIのACCESS TOKENを取得する
作成していない場合は下記を参考に作成してください！
:::details DeepL APIのアカウントを作成する
不正利用防止のため無料アカウントでもクレジットカード必須となっております。
1. 
:::
## 設定
1. ダウンロードした workflow ファイルをダブルクリック
![](https://i.gyazo.com/5297a04eba8b83a385578f097656131f.png)

2. 赤枠内に DEEPL のACCESS TOKENを貼り付けて「import」押下
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

4. Hotkeyに割り当てたいコマンドが反映されていれば完了
![](https://i.gyazo.com/8224c20ae6f3e901a4acfe4b4cef8ef9.png)

:::

## 使い方
* 翻訳結果を表示したい
* 翻訳結果をクリップボードにコピーする

## 言語の変更の仕方

## この記事のリポジトリ
Node.jsで作成しました。
https://github.com/sugayutokyo/alfred_deepl_transrator

## 終わりに
不具合やこんな機能がほしいなどあれば、コメントなどもらえると嬉しいです！！
直近ではDeepLの有料プランへの対応を考えております。

## 参考
https://blog.chick-p.work/nodejs-aflred-workflow/