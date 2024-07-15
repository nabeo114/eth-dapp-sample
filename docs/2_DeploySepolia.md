# 2: SepoliaテストネットにContractをデプロイ

[1.ERC20Token Contractの作成](./1_CreateERC20Token.md)で作成したMyTokenをSepoliaテストネットにデプロイする手順を説明します。
Sepoliaテストネットにデプロイするためには手数料となるSepoliaETHが必要です。そのため、まず最初にWalletアプリであるMetamaskをインストールし、SepoliaETHを入手する手順を説明します。

次に、Sepoliaに接続するためにInfura.ioのサービスに登録して利用する手段を説明します。

最後にTruffleにSepoliaと接続するためのネットワーク設定と、Infura.ioとMetamaskのアカウントを利用する設定を追加して、SepoliaにMyTokenをデプロイする手順を説明します。

### 1. **ChromeにMetamaskプラグインをインストール**:
   - [Metamaskの公式ウェブサイト](https://metamask.io/download.html)またはChromeウェブストアからMetamaskプラグインをダウンロードしてインストールします。
   - Metamaskを開き、アカウントを作成します。アカウント作成後に秘密鍵を取得してメモしておきます。この秘密鍵は後でネットワーク情報の設定に使用します。

### 2. **SepoliaテストネットのfaucetからETHを入手**:
   - MetamaskでSepoliaテストネットを選択します。
   - [Sepolia faucet](https://sepoliafaucet.com/)にアクセスし、Metamaskのアドレスを入力してETHをリクエストします。注意: このfaucetは1日1回しかETHを入手できないため、必要に応じて計画的に行動してください。

### 3. **Infura.ioのアカウントを作成**:
- [Infura.io](https://infura.io/) にアクセスし、新しいアカウントを作成します。
- アカウント作成後、Infuraダッシュボードで新しいAPI Keyを作成してメモしておきます。このAPI Keyは後でネットワーク情報の設定に使用します。

### 4. **hardhat.config.jsにSepoliaテストネットのネットワーク情報を設定**:
- `.env`という名前の新しいファイルを作成し、以下の内容をコピーして貼り付けます。
```bash
$ touch .env
```
```
INFURA_API_KEY='your api key'
PRIVATE_KEY='your private key'
```
- `.env`ファイルに、メモしておいたAPI Keyと秘密鍵を設定します。

### 5. **Hardhatコマンドを用いてSepoliaにMyTokenをデプロイ**:
 
- ターミナルを開き、packages/contractsに移動します。
- 以下のコマンドを実行してMyTokenをSepoliaテストネットにデプロイします。

```bash
$ npx hardhat run scripts/deploy.js --network sepolia
```

- ターミナルに表示されるログから、Contract Addressをメモしておく。

### 6. **etherscanで確認**:
- [Sepolia Testnet Etherscan](https://sepolia.etherscan.io/)にアクセスし、Contract Addressを使ってデプロイメントを確認します。

![etherscan](images/sepolia_etherscan.png)

[1.ERC20Token Contractの作成](./1_CreateERC20Token.md) &lt;&lt;prev next&gt;&gt; - [3.SimpleなDappsの作成](./3_CreateSimpleDapps.md)
