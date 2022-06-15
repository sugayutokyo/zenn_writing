---
title: "web3.jsでローカル環境にデプロイしたコントラクトをNext.js(Typescript)で使ってみる"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'web3', 'solidity', 'typescript']
published: true
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
## Truffle(トリュフ)とは
* Solidityの開発用フレー厶ワーク
* スマートコントラクトのビルド(コンパイル)、テスト、デプロイをサポート

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
## Ganache(ガナッシュ)とは
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
ZennCoinのスマートコントラクトがデプロイされていることが確認できる
![](https://i.gyazo.com/ffb9d8a786427d3a1f760188f873edfb.png)

# Next.jsで実際にスマートコントラクトを使ってみる
## スマートコントラクトをNext.jsで使用する流れ
![](https://i.gyazo.com/fcbc29d27bcc692396466e506a147474.png)

Application Binary Interface(ABI)はコントラクトに接続するインターフェースです。Solidityファイルをbuildして作成されるJSONファイル内にABIが存在し、ABIを経由してコントラクトを操作することができます。本記事ではNext.jsからABIを経由してコントラクトを操作します。
## 準備
### web3.jsをインストール
```
$ npm i web3
```

### TypeChainを使って型ファイルの生成
ABIはSolidityファイルをbuildして作成されるJSONファイルに存在するため、Solidityファイルを変更するたびに型情報が変更になる可能性があります。ABIの型情報はJSONファイルのABIから生成してあげる必要があり、typechainというライブラリによって実現できます。
1. ABIの型生成のために下記ライブラリをインストール
```
$ npm install -D typechain @typechain/web3-v1
$ npm install web3-utils
```

2. typechainを実行するためにscriptsに下記内容を追加
```diff js:package.json
{
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
-    "start": "next start"
+    "start": "next start",
+    "typechain": "typechain --target web3-v1 --out-dir types/abi './build/contracts/**/*.json'"
  },
...
```

3. typechainを実行し、下記画像のようにtypes/abi配下にtypeのファイルが作成されることを確認
```sh
$ npm run typechain
```
![](https://i.gyazo.com/be0b2f6d5e1a1f81ce137503f76332eb.png)

4. index.jsを下記コードに書き換えて実際に型付けがうまくいくかどうか確認
```js:papes/index.tsx
import type { NextPage } from 'next';
import Web3 from 'web3';
import { AbiItem } from 'web3-utils';
import ZCContract from '../build/contracts/ZennCoin.json';
import { ZennCoin } from '../types/abi/ZennCoin';

// プロバイダの設定
const web3 = new Web3(new Web3.providers.HttpProvider(`http://127.0.0.1:7545`));

// コントラクトのアドレス
const address = '0x8F4D574EFe77e00af32C54d2A0D07F7C53cb56bF';

const ABI = ZCContract.abi as any as AbiItem;
// コントラクトのインスタンス
const contract = new web3.eth.Contract(ABI, address) as unknown as ZennCoin;

const Home: NextPage = () => {
  return <div></div>;
};

export default Home;
```

* buildで生成されたJSONファイルをインポートする
```ts
import ZCContract from '../build/contracts/ZennCoin.json';
```

* インポートしたJSONファイルからabiを抽出
```ts
const ABI = ZCContract.abi as any as AbiItem;
```

* 以下のコードはhttp://127.0.0.1:7545を指定することでGanacheの開発用チェーンを指定しています。テストネットやメインネットを使用したい時にはhttp://127.0.0.1:7545の部分をそれぞれ変更すればOK
```ts
const web3 = new Web3(new Web3.providers.HttpProvider(`http://127.0.0.1:7545`));
```

* コントラクトのアドレスは以下コードで指定している
```ts
const address = '0x8F4D574EFe77e00af32C54d2A0D07F7C53cb56bF';
```
コントラクトのアドレスはGanacheのCONTRACTSタブのZennCoinのADDRESSをコピーする
![](https://i.gyazo.com/238d5bb9e133983009aa978ee0ac6066.png)


* 以下コードでコントラクトのインスタンスを生成している。コントラクトに関する操作はこのインスタンスを通して行う
```ts
const contract = new web3.eth.Contract(ABI, address) as unknown as ZennCoin;
```

5. 下記のようにcontractで使用できるメソッドが予測候補に出てきたら成功
![](https://i.gyazo.com/83cdc2d56a848794f7e4dd772804ccb0.gif)

## Next.jsからABIを経由してコントラクトを操作
### まずはアカウント情報を取得してみる
1. pages/index.tsxを下記のように修正
```diff tsx:pages/index.tsx
...
const Home: NextPage = () => {
+  (async () => {
+    const accountsWeb3 = await web3.eth.getAccounts();
+    console.log(accountsWeb3);
+  })();
  return <div></div>;
};
...
```

2. chromeでlocalhostにアクセスして下記のようにコンソールにアカウントのADDRESSが表示されることが確認できればOK
![](https://i.gyazo.com/990da0844ac232e06a907264e13a4a4c.png)
表示されているADDRESSがGanacheのものと同じであることを確認する
![](https://i.gyazo.com/c00ddf2f2b92826b9e67e0d55004a658.png)

### ETHとZennCoinの残高を取得する
1. pages/index.tsxを下記のように修正
今回は2つのアカウント内でZennCoinを移動させるため、2つのアカウント(UserAとUserB)のETHとZennCoinの残高を取得します。
本記事ではWeb3技術の共有を主としているため、Reactについてのコードの解説は行いません。
```diff tsx:pages/index.tsx
import type { NextPage } from 'next';
+ import { useState } from 'react';
import Web3 from 'web3';
import { AbiItem } from 'web3-utils';
import ZCContract from '../build/contracts/ZennCoin.json';
import { ZennCoin } from '../types/abi/ZennCoin';

// プロバイダの設定
const web3 = new Web3(new Web3.providers.HttpProvider(`http://127.0.0.1:7545`));

// コントラクトのアドレス
const address = '0x8F4D574EFe77e00af32C54d2A0D07F7C53cb56bF';

const ABI = ZCContract.abi as any as AbiItem;
// コントラクトのインスタンス
const contract = new web3.eth.Contract(ABI, address) as unknown as ZennCoin;

+ const walletAddressUserA = '0x7D4d7a0da0e8e1Dc90a86fDB82882a94190d89D6';
+ const walletAddressUserB = '0x95a1D1A9fA7280E8A98c288a7bFD69EFdEFcD390';

const Home: NextPage = () => {
-  (async () => {
-    const accountsWeb3 = await web3.eth.getAccounts();
-    console.log(accountsWeb3);
-  })();
-  return <div></div>;
+  const [balanceZcUserA, setBalanceZcUserA] = useState(''); // ZennCoin残高 UserA
+  const [balanceEthUserA, setBalanceEthUserA] = useState(''); // ETH残高 UserA
+  const [balanceZcUserB, setBalanceZcUserB] = useState(''); // ZennCoin残高 UserB
+  const [balanceEthUserB, setBalanceEthUserB] = useState(''); // ETH残高 UserB
+  const getBalance = async (userType: string) => {
+    if (userType === 'a') {
+      setBalanceZcUserA(await contract.methods.balanceOf(walletAddressUserA).call());
+      setBalanceEthUserA(await web3.eth.getBalance(walletAddressUserA));
+    } else if (userType === 'b') {
+      setBalanceZcUserB(await contract.methods.balanceOf(walletAddressUserB).call());
+      setBalanceEthUserB(await web3.eth.getBalance(walletAddressUserB));
+    }
+  };
+  
+  return (
+    <div className="m-5">
+      <h2>UserA Info</h2>
+      {balanceZcUserA ? (
+        <table className="table-auto">
+          <thead>
+            <tr>
+              <th className="px-4 py-2">ZennCoin Balance</th>
+              <th className="px-4 py-2">ETH Balance</th>
+            </tr>
+          </thead>
+          <tbody>
+            <tr>
+              <td className="border px-4 py-2">{balanceZcUserA}</td>
+              <td className="border px-4 py-2">{balanceEthUserA}</td>
+            </tr>
+          </tbody>
+        </table>
+      ) : (
+        <div>「UserA 残高を取得」を押してください</div>
+      )}
+      <h2>UserB Info</h2>
+      {balanceZcUserB ? (
+        <table className="table-auto">
+          <thead>
+            <tr>
+              <th className="px-4 py-2">ZennCoin Balance</th>
+              <th className="px-4 py-2">ETH Balance</th>
+            </tr>
+          </thead>
+          <tbody>
+            <tr>
+              <td className="border px-4 py-2">{balanceZcUserB}</td>
+              <td className="border px-4 py-2">{balanceEthUserB}</td>
+            </tr>
+          </tbody>
+        </table>
+      ) : (
+        <div>「UserB 残高を取得」を押してください</div>
+      )}
+      <button
+        className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
+        onClick={() => getBalance('a')}>
+        UserA 残高を取得
+      </button>
+      <button
+        className="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded ml-5"
+        onClick={() => getBalance('b')}>
+        UserB 残高を取得
+      </button>
+    </div>
  );
};

export default Home;

```

* 下記コードでwalletAddressを2つ指定している(GanacheのACCOUNTSタブの中のINDEX 0とINDEX 1のものを本記事では使用)
```ts
const walletAddressUserA = '0x7D4d7a0da0e8e1Dc90a86fDB82882a94190d89D6';
const walletAddressUserB = '0x95a1D1A9fA7280E8A98c288a7bFD69EFdEFcD390';
```
![](https://i.gyazo.com/96280cff464bd0daee1bac1990e4cf33.png)
※2回デプロイしたのでETHの残高が少なくなっています。分かりづらくて申し訳ありません。
本記事を順番に進めているのであれば99.97と表示されるはずです！

* contractインスタンスから指定したAddressのZennCoinの残高を取得する
```ts
contract.methods.balanceOf(walletAddressUserA).call()
```

* web3インスタンスから指定したAddressのETHの残高を取得する
```ts
web3.eth.getBalance(walletAddressUserA)
```
2. 動作確認する
「UserA 残高を取得」、「UserB 残高を取得」を謳歌することで下記GIFのように残高を取得することができたらOK
![](https://i.gyazo.com/16eb5a9cfbdb2aac99c37a116a1f7554.gif)

### ZennCoinを移動させる
1. pages/index.tsxを下記のように修正
```diff tsx:pages/index.tsx
const ABI = ZCContract.abi as any as AbiItem;
// コントラクトのインスタンス
const contract = new web3.eth.Contract(ABI, address) as unknown as ZennCoin;

const walletAddressUserA = '0x7D4d7a0da0e8e1Dc90a86fDB82882a94190d89D6';
const walletAddressUserB = '0x95a1D1A9fA7280E8A98c288a7bFD69EFdEFcD390';

+ const contractFromA = new web3.eth.Contract(ABI, address, { from: walletAddressUserA }) as unknown as ZennCoin;

const Home: NextPage = () => {
  ...
  const getBalance = async (userType: string) => {
    if (userType === 'a') {
      setBalanceZcUserA(await contract.methods.balanceOf(walletAddressUserA).call());
      setBalanceEthUserA(await web3.eth.getBalance(walletAddressUserA));
    } else if (userType === 'b') {
      setBalanceZcUserB(await contract.methods.balanceOf(walletAddressUserB).call());
      setBalanceEthUserB(await web3.eth.getBalance(walletAddressUserB));
    }
  };

+  const transferZennCoin = async () => {
+    await contractFromA.methods.transfer(walletAddressUserB, 1000).send();
+  };

  return (
    <div className="m-5">
      <h2>UserA Info</h2>
      ...
      <h2>UserB Info</h2>
      ...
      <button
        className="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded ml-5"
        onClick={() => getBalance('b')}>
        UserB 残高を取得
      </button>
+     <button
+       className="bg-orange-500 hover:bg-orange-700 text-white font-bold py-2 px-4 rounded ml-5"
+       onClick={transferZennCoin}>
+       Transfer ZennCoin From A To B
+     </button>
    </div>
  );
};

```
* 通貨を移動させるときは下記のようにfromを指定してcontractインスタンスを作成する必要がある
```ts
const contractFromA = new web3.eth.Contract(ABI, address, { from: walletAddressUserA }) as unknown as ZennCoin;
```

* transferメソッドの第1引数には送る相手、第2引数には移動する通貨量を指定する
```ts
contractFromA.methods.transfer(walletAddressUserB, 1000).send()
```

2. 移動前の残高を確認する
![](https://i.gyazo.com/6161c52e92647945a25ab425ccea4c3e.png)

3. 「Transfer ZennCoin From A To B」を押下後「UserA 残高を取得」「UserB 残高を取得」をそれぞれ押下して下記画像のようにZennCoinが1000移動していればOK
![](https://i.gyazo.com/34b70d1be72291324c6a5c85bd844276.png)

## エラーハンドリング
* Error: Invalid JSON RPC response: ""
![](https://i.gyazo.com/c4d71b110fc3fc7790482065db6846e7.png)
原因: Ganacheを起動していないと起きてしまうエラー
解決策: Ganacheを起動することで解決

# 最後に
今回はスマートコントラクトを作成し、独自通貨の取得、移動を行いました。私が所属している株式会社UPBONDではWeb3技術を使った開発を行っています。今後もWeb3技術で開発していく中で手に入れた知見を記事にしていきます。
記事の中で間違っている箇所がありましたらコメントをいただけたら幸いです。

# 参考
https://qiita.com/kyrieleison/items/8ef926faa4defa8fe930
https://zenn.dev/linnefromice/articles/create-simple-dapps-with-hardhat-and-react-ts
https://tech.mobilefactory.jp/entry/2019/12/04/163000
