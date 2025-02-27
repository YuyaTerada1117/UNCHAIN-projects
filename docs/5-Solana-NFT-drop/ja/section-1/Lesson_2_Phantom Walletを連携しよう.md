### 🤖 ローカル開発環境を設定する

※ GitHub アカウントの初期設定がお済みでない方は、アカウント設定を行ってから先へお進みください。

まず、 [この GitHub リンク](https://github.com/shiftbase-xyz/nft-drop-starter-project) にアクセスして、ページの右上にある[Fork]ボタンを押してください。

このリポジトリをフォークすると、自分の GitHub に同一のリポジトリがコピーされます。

次に新しくフォークされたリポジトリをローカルに保存します。

「Code」ボタンをクリックして、そのリンクをコピーしてください。

![](/public/images/5-Solana-NFT-drop/section1/1_1_1.png)

最後に、ターミナルに移動し、`cd` コマンドでプロジェクトが存在するディレクトリまでいき、次のコマンドを実行します。

```txt
git clone YOUR_FORKED_LINK
```

これでローカル開発環境の準備は完了です。

### 🔌 Phantom Wallet を使用してウォレット接続ボタンを作成する

このプロジェクトでは、[ Phantom Wallet ](https://phantom.app/)という、Solana の NFT 取扱に優れたウォレットを使用します。

まずは拡張機能をダウンロードして Phantom Wallet をセットアップしてください。

Phantom Wallet は **Chrome**、 **Brave**、 **Firefox**、および **Edge** をサポートしています。

Chrome の方は[ こちら ](https://chrome.google.com/webstore/detail/phantom/bfnaelmomeimhlpmgjnjophhpkkoljpa)から Phantom Wallet をインストールすることがきます。

Phantom Wallet のネットワークを Devnet に変更してください。

- 「設定」→「ネットワークの変更」→「Devnet」から変更できます。

今回作成する Candy Machine は Devnet 上にあるので、ウォレットも Devnet に変更する必要があります。

![無題](/public/images/5-Solana-NFT-drop/section3/3_1_1.png)

※ 本プロジェクトでは Brave と Chrome でのみ動作が確認できます。

### 👻 Solana オブジェクトを設定する

ユーザーの Phantom Wallet を、作成する Web アプリケーションと接続する必要があります。

エディタより、`src/App.js` ファイルを開いてください。これはアプリケーションのメインのエントリポイントになるファイルです。

Phantom Wallet 拡張機能がインストールされている場合は、`window` オブジェクトに `solana` という名前の特別なオブジェクトが自動的に代入されます。

ミントする前に、`solana` が代入されているか確認する必要があります。存在しない場合はダウンロードするようにユーザーに指示しましょう。

`App.js` を下記の通り変更します。

```jsx
import React, { useEffect } from "react";
import "./App.css";
import twitterLogo from "./assets/twitter-logo.svg";

// 定数の宣言
const TWITTER_HANDLE = "あなたのTwitterハンドル";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  // Actions

  /*
   * 関数を宣言します
   */
  const checkIfWalletIsConnected = async () => {
    try {
      const { solana } = window;

      if (solana && solana.isPhantom) {
        console.log("Phantom wallet found!");
      } else {
        alert("Solana object not found! Get a Phantom Wallet 👻");
      }
    } catch (error) {
      console.error(error);
    }
  };

  /*
   * コンポーネントが最初にマウントされたら、Phantom Walletが
   * 接続されているかどうかを確認しましょう。
   */
  useEffect(() => {
    const onLoad = async () => {
      await checkIfWalletIsConnected();
    };
    window.addEventListener("load", onLoad);
    return () => window.removeEventListener("load", onLoad);
  }, []);

  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header">🍭 Candy Drop</p>
          <p className="sub-text">NFT drop machine with fair mint</p>
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

`App.js` を分解して説明します。

```jsx
const checkIfWalletIsConnected = async () => {
  try {
    const { solana } = window;

    if (solana && solana.isPhantom) {
      console.log("Phantom wallet found!");
    } else {
      alert("Solana object not found! Get a Phantom Wallet 👻");
    }
  } catch (error) {
    console.error(error);
  }
};
```

関数 `checkIfWalletIsConnected` は、DOM の `window` オブジェクトをチェックして 、Phantom Wallet が `solana` オブジェクトを挿入したかどうかを確認します。

`solana` オブジェクトが存在しているか、またそれが Phantom Wallet であるかどうかを確認しています。

```jsx
useEffect(() => {
  const onLoad = async () => {
    await checkIfWalletIsConnected();
  };
  window.addEventListener("load", onLoad);
  return () => window.removeEventListener("load", onLoad);
}, []);
```

最後に、これを呼び出す必要があります。

React では、 2 番目のパラメータ( `[]` )が空の場合、コンポーネントをマウント時に `useEffect` hook が 1 回呼び出されます。

これで、私たちの Web アプリケーションにアクセスするとすぐに、Phantom Wallet がインストールされているかどうかを確認できます。これは **非常に重要**な機能です。

最後に、あなたの Twitter ハンドルを以下に貼り付けるのをお忘れなく！

```jsx
const TWITTER_HANDLE = "あなたのTwitterハンドル";
```

### 🔒 ユーザーのアカウントにアクセスする

一度、ブラウザでインタフェースを確認してみましょう。

1\. ターミナルを開き、`cd` で `app` フォルダまで移動します。

2\. `npm install` を実行します。

3\. `npm run start` を実行します。

これを実行すると、Web アプリケーションのコンソールに `Phantom Wallet found!` という行が表示されるはずです。

![無題](/public/images/5-Solana-NFT-drop/section1/1_1_2.png)

次に、ユーザーのウォレットにアクセスすることが**許可**されているか確認する必要があります。アクセスが許可されていると、Solana プログラムの関数にアクセスできます。

Phantom Wallet は、すべての Web アプリケーションにウォレット情報を提供する訳ではなく、許可した Web アプリケーションだけに許可します。

Web アプリケーションで最初に行う必要があるのは、ユーザーが Web アプリケーションでウォレットを使用する許可を与えているか確認することです。

これはユーザーが「ログイン」しているかどうかを確認するようなものです。

ここで `checkIfWalletIsConnected` 関数にもう 1 行追加する必要があります。以下のコードを修正してください。

```jsx
const checkIfWalletIsConnected = async () => {
  try {
    const { solana } = window;

    if (solana && solana.isPhantom) {
      console.log("Phantom wallet found!");

      /*
       * "solana"オブジェクトは、ユーザーのウォレットに直接
       * 接続できる機能を提供しています。
       * 下記からコードを修正してください。
       */
      const response = await solana.connect({ onlyIfTrusted: true });
      console.log("Connected with Public Key:", response.publicKey.toString());
    } else {
      alert("Solana object not found! Get a Phantom Wallet 👻");
    }
  } catch (error) {
    console.error(error);
  }
};
```

`connect` を呼び出すだけで、Web アプリケーションがそのウォレットに関する情報へのアクセスを許可されていることを Phantom Wallet に通知できます。

`onlyIfTrusted` プロパティは、ユーザーがすでにウォレットをアプリケーションに接続している場合、`true` になります。

別の接続ポップアップを表示せずに、すぐにデータを pull します。詳細は [fantom の公式ドキュメント](https://docs.phantom.app/integrating/establishing-a-connection#eagerly-connecting)をご覧ください。

以上です！

現時点では、コンソールのログに `Phantom wallet found!` と表示されるだけです。

もしコンソールに `UserRejectedRequest` エラーが表示されても心配しないでください。

現段階のみの問題で、`connect` メソッド内に `onlyIfTrusted：true` パラメータを追加したためです。

`onlyIfTrusted` パラメータが `true` に設定された `connect` メソッドは 、ユーザーがウォレットと Web アプリケーション間の接続をすでに承認している場合にのみ実行されます。次のセクションで修正します。

ターミナルを閉じるときは、以下のコマンドが使えます ✍️

- Mac: `ctrl + c`
- Windows: `ctrl + shift + w`

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discord の `#section-1` で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の 3 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

次のレッスンに進んで、開発を進めましょう 🎉
