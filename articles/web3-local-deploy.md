---
title: "web3.jsでローカル環境にデプロイしたコントラクトをNext.jsで使ってみる"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'web3', 'solidity']
published: false
---

# 本記事の対象者
* Web3技術に興味がある
* Next.jsにWeb3技術を導入したい
* 独自通貨を作成してみたい

# 本記事のリポジトリ
https://github.com/sugayutokyo/next-zenn-web3
ぜひ参考にしてください！

# Next.jsの環境構築
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

本記事では prettier を導入しています。導入したい方は以下のファイルを参考に設定してください！

```js:.prettierrc
{
  "printWidth": 120,
  "tabWidth": 2,
  "semi": true,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "jsxSingleQuote": false,
  "trailingComma": "all",
  "bracketSpacing": true,
  "jsxBracketSameLine": true,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

# Truffleの環境構築
## Truffleとは
Solidityの開発用フレー厶ワーク。スマートコントラクトのビルド(コンパイル)、テスト、デプロイをサポート

## Truffleの導入
```sh
# truffleをインストールする(既にインストールされている場合は不要)
$ npm install -g truffle
# truffleのバージョンを確認する
$ truffle
Truffle v5.5.17 - a development framework for Ethereum

Usage: truffle <command> [options]

Commands:
  truffle build      Execute build pipeline (if configuration present)
  truffle compile    Compile contract source files
  truffle config     Set user-level configuration options
  truffle console    Run a console with contract abstractions and commands
                     available
...
# 初期化を行う
$ truffle init
```
下記画像のようにcontracts, migrations, testのディレクトリ、truffle-config.jsファイルが作成されればOK
![](https://i.gyazo.com/d0d7aeb0484e9d990729073050da3e81.png)

# Ganacheによるローカル環境の構築
## Ganacheとは
TruffleチームがローンチしたEthereum開発環境。ローカルで開発用のチェーンを構築できて、GUIでブロックやトランザクションを参照することができる
## Ganacheのダウンロード
https://trufflesuite.com/ganache/

## WorkSpaceを作成
下記手順でWorkSpaceを作成して先ほど作成したtruffleと紐付けを行う
1. 「NEW WORKSPACE」押下
![](https://i.gyazo.com/14e6a24ef0f3feb86bed0c1385f8ea7c.png)

2. WORKSPACE NAMEに「ZENN TOKEN」と入力する
![](https://i.gyazo.com/370204e181a2086c04d9cf2714547d1d.png)

3. 「ADD PROJECT」押下
![](https://i.gyazo.com/546553d5908ae210b93c52382047840f.png)

4. `truffle-config.js`を選択する
※ `truffle init`実行時に作成されるはず
![](https://i.gyazo.com/fa14e68ebfbfb260a6bc4fb057d98d35.png)

5. 「SAVE WORKSPACE」押下
![](https://i.gyazo.com/eb3b691ca81a21b978a94abe2865297f.png)

6. 下記画面が表示されればOK
![](https://i.gyazo.com/ede2c9033efd9e30f7fa1b8ef70b3c1c.png)

# スマートコントラクトを作成する
スマートコントラクトで独自通貨(ZennCoin)を作成します！スマートコントラクトで使用する言語はSolidityで拡張子は.solです。
https://solidity-jp.readthedocs.io/ja/latest/

VSCodeを使っているのであれば下記拡張機能を導入することをお勧めします！(ハイライトなど便利)
![](https://i.gyazo.com/536e55161358871212a8da0a9496b3fd.png)

## 全体の流れ
![](https://i.gyazo.com/f3969925d4b68bd7aa1e8c588851a4e7.png)

① コントラクトを作成
Solidityでスマートコントラクトを実装します。

② build
作成したスマートコントラクトはそのままでは実行できません。buildしてJSONファイルに変換する必要があります。buildはmigrateファイルを元にして行われるためmigrateファイルを作成する必要があります。

③ deploy
buildで作成されたJSONファイルをGanacheの開発用チェーンにdeployする

## ① コントラクトを作成
1. 通貨用のERC20プロトコルを使用するためにopenzeppelinをインストールする
```sh
$ npm i @openzeppelin/contracts
```

2. contractファイルを作成
```sh
$ truffle create contract ZennCoin
```

3. 下記コードに書き換え
```sol:contracts/ZennCoin.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract ZennCoin is ERC20 {
  constructor(uint256 initialSupply) ERC20("ZENC", "ZEC") {
    _mint(msg.sender, initialSupply);
  }
}
```

* `import "@openzeppelin/contracts/token/ERC20/ERC20.sol";`でERC20プロトコルのインポート
* `contract ZENNToken is ERC20`でERC20プロトコルを継承することによってERC20プロトコルに準拠した通貨を作成することができる

## ② build
1. buildに必要なmigrateファイルを作成
```sh
$ truffle create migration zennCoin
```

2. 下記コードに書き換え
```js:1655110571_zenn_coin.js
const zennCoin = artifacts.require('ZennCoin');

module.exports = function (deployer) {
  const initSupply = 10000000000;

  deployer.deploy(zennCoin, initSupply);
};
```
* ファイル名の最初の数字は作成時に割り当てられるので異なっていても問題ありません
* `const zennCoin = artifacts.require('ZennCoin');`で作成したスマートコントラクトを紐づけている
* `const initSupply = 10000000000;`でコインの供給量を指定することができます

3. build
下記のようにCompiled successfullyとなってbuild/contractsにjsonファイルが作成されていればOK
```
$ truffle build
Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/ZennCoin.sol
> Compiling @openzeppelin/contracts/token/ERC20/ERC20.sol
> Compiling @openzeppelin/contracts/token/ERC20/IERC20.sol
> Compiling @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol
> Compiling @openzeppelin/contracts/utils/Context.sol
> Artifacts written to /Users/sugayutokyo/Developer/workplace/private/next-zenn-web3/build/contracts
> Compiled successfully using:
   - solc: 0.8.14+commit.80d49f37.Emscripten.clang
```
![](https://i.gyazo.com/facc1f721599b95b2b10801e739aa39a.png)

## ③ deploy
1. deploy先を設定する
デプロイ先をGanacheの開発用チェーンに設定するためにtruffle-config.jsを変更します
networksの中のdevelopmentのコメントアウトを外し、hostとportをGanacheのRPC SERVERの値と同じにします。
```diff js:truffle-config.js
  networks: {
    // Useful for testing. The `development` name is special - truffle uses it by default
    // if it's defined here and no other network is specified at the command line.
    // You should run a client (like ganache, geth, or parity) in a separate terminal
    // tab if you use this network and you must also set the `host`, `port` and `network_id`
    // options below to some value.
    //
-    // development: {
-    //  host: "127.0.0.1",     // Localhost (default: none)
-    //  port: 8545,            // Standard Ethereum port (default: none)
-    //  network_id: "*",       // Any network (default: none)
-    // },
    
+    development: {
+     host: "127.0.0.1",     // Localhost (default: none)
+     port: 7545,            // Standard Ethereum port (default: none)
+     network_id: "*",       // Any network (default: none)
+    },

    //
    // An additional network, but with some advanced options…
    // advanced: {
    //   port: 8777,             // Custom port
    //   network_id: 1342,       // Custom network
    //   gas: 8500000,           // Gas sent with each transaction (default: ~6700000)
    //   gasPrice: 20000000000,  // 20 gwei (in wei) (default: 100 gwei)
    //   from: <address>,        // Account to send transactions from (default: accounts[0])
    //   websocket: true         // Enable EventEmitter interface for web3 (default: false)
    // },
    //
```
![](https://i.gyazo.com/2f75d7f5026feadcafb749dae577f5f0.png)

2. deploy前のGanacheの開発用チェーンの状態を確認
* アカウント
![](https://i.gyazo.com/9a254cdb2e2d0a787ad184a5aaf0a3c1.png)
* トランザクション
![](https://i.gyazo.com/5155440d3ea6ef7597eb73aa6b424a6e.png)
* コントラクト
![](https://i.gyazo.com/7d99aeb6be1d6d2a07d7b940d6457a68.png)

3. deploy
```
$ truffle deploy

Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.


Starting migrations...
======================
> Network name:    'development'
> Network id:      5777
> Block gas limit: 6721975 (0x6691b7)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x8ba26437e4520ad95dfd187efde063c4f7512c0ea0833bdec09d6b685aa05b31
   > Blocks: 0            Seconds: 0
   > contract address:    0xd18a0D7149717D50316d921B145E1E40A76D70d6
   > block number:        1
   > block timestamp:     1655124819
   > account:             0x7D4d7a0da0e8e1Dc90a86fDB82882a94190d89D6
   > balance:             99.99502292
   > gas used:            248854 (0x3cc16)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00497708 ETH

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00497708 ETH


1655110571_zenn_coin.js
=======================

   Deploying 'ZennCoin'
   --------------------
   > transaction hash:    0x5c132c121ee05deabe1db9b35da0cf3b3516881f99c4ec9145fb339c94f67d20
   > Blocks: 0            Seconds: 0
   > contract address:    0x932546ed94E905216e34c417602a93bC0DEc07D5
   > block number:        3
   > block timestamp:     1655124820
   > account:             0x7D4d7a0da0e8e1Dc90a86fDB82882a94190d89D6
   > balance:             99.9707734
   > gas used:            1169963 (0x11da2b)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.02339926 ETH

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.02339926 ETH

Summary
=======
> Total deployments:   2
> Final cost:          0.02837634 ETH
```

4. deploy成功後のGanacheの開発用チェーンの状態を確認
* アカウント
ETHからガス代分減っている。今回のdeployにおけるガス代は`truffle deploy`時のFinal cost: 0.02837634 ETHなので、100ETH - 約0.028 = 99.97ETHになっている。
![](https://i.gyazo.com/5fa715355c55635ddaeaa8c4a065a7b9.png)
* トランザクション
トランザクションの履歴が確認できる
![](https://i.gyazo.com/357a9d2944ed41e50d5c05e1666e4062.png)
* コントラクト

![](https://i.gyazo.com/ffb9d8a786427d3a1f760188f873edfb.png)

# Next.jsで実際にスマートコントラクトを使ってみる

# 最後に
私が所属している株式会社UPBONDではWeb3技術を使った開発を行なっています。今後もWeb3技術で開発していく中で記事にしていきます。

# 参考
https://qiita.com/kyrieleison/items/8ef926faa4defa8fe930