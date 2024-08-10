# 1: ERC20Token Contractの作成

HardhatとOpenZeppelinを使用してERC20トークンを作成しする手順を説明します。また、作成したERC20トークンをHardhatから操作する方法も説明します。

### 1. **プロジェクトのセットアップ:**

1.1 **Hardhatプロジェクトの初期化:**
```bash
$ cd packages/contracts
$ yarn add --dev @nomicfoundation/hardhat-toolbox @nomicfoundation/hardhat-ignition @nomicfoundation/hardhat-ignition-ethers @nomicfoundation/hardhat-network-helpers @nomicfoundation/hardhat-chai-matchers @nomicfoundation/hardhat-ethers @nomicfoundation/hardhat-verify chai@4 ethers hardhat-gas-reporter solidity-coverage @typechain/hardhat typechain @typechain/ethers-v6
```

1.2 **OpenZeppelinのインストール:**
```bash
$ yarn add --dev @openzeppelin/contracts
```

1.3 **その他ツールのインストール:**
```bash
$ yarn add --dev dotenv
```

### 2. **ERC20トークンの作成:**

2.1 **トークンコントラクトの作成:**

`contracts/MyToken.sol`という名前の新しいファイルを作成し、以下の内容をコピーして貼り付けます。
```bash
$ mkdir contracts
$ touch contracts/MyToken.sol
```
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    address public owner;
    constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
        owner = msg.sender;
        _mint(owner, initialSupply);
    }
}
```

2.2 **トークンコントラクトのコンパイル:**

以下のコマンドを実行してMyToken Contractのコンパイルを実行します。
```bash
$ npx hardhat compile
```

2.3 **トークンコントラクトのテスト:**

`test/MyToken.js`という名前の新しいファイルを作成し、以下の内容をコピーして貼り付けます。
```bash
$ mkdir test
$ touch test/MyToken.js
```
```javascript
const { expect } = require("chai");
const { ethers } = require("hardhat");
const {
    loadFixture
} = require("@nomicfoundation/hardhat-toolbox/network-helpers");

describe("Token contract", function () {
    async function deployTokenFixture() {
    const [owner, other] = await ethers.getSigners();
    const ownerAddress = await owner.getAddress();
    const otherAddress = await other.getAddress();
    const initialSupply = 1000;

    const myToken = await ethers.deployContract("MyToken", [initialSupply]);
    await myToken.waitForDeployment();
        const myTokenAddress = myToken.address;
        return { myToken, myTokenAddress, initialSupply, owner, ownerAddress, other ,otherAddress };
    }

    describe("Deployment", function () {
        it("Should mint owner 1000 tokens when deployed", async function () {
            const { myToken, ownerAddress, initialSupply } = await loadFixture(deployTokenFixture);
            const ownerBalance = await myToken.balanceOf(ownerAddress)
            expect(ownerBalance).to.equal(initialSupply);
        });
        it("Should set the right owner", async function () {
            const { myToken, ownerAddress } = await loadFixture(deployTokenFixture);
            expect(await myToken.owner()).to.equal(ownerAddress);
        });
    });
});
```

以下のコマンドを実行してMyToken Contractのテストを実行します。
```bash
$ npx hardhat test
```

### 3. **デプロイスクリプトの作成:**

3.1 **デプロイスクリプの作成:**

`scripts/deploy.js`という名前の新しいファイルを作成し、以下の内容をコピーして貼り付けます。
```bash
$ mkdir scripts
$ touch scripts/deploy.js
```
```javascript
const { ethers } = require("hardhat");

async function main() {
    const myToken = await ethers.deployContract("MyToken", [1000000]);
    await myToken.waitForDeployment();

    console.log("MyToken deployed to:", await myToken.getAddress());
    console.log("MyToken deployed by:", await myToken.owner());

    const txReceipt = await myToken.deploymentTransaction().wait();

    console.log("> transaction hash:", txReceipt.hash);
    console.log("> contract address:", myToken.target);
    console.log("> block number:", txReceipt.blockNumber);
    console.log("> account:", txReceipt.from);
    console.log("> balance:", ethers.formatEther(await ethers.provider.getBalance(txReceipt.from)));
    console.log("> gas price:", ethers.formatUnits(txReceipt.gasPrice, 'gwei'), "gwei");
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

### 4. **Hardhat開発ノードのセットアップとデプロイ:**

4.1 **開発ノードの起動:**

以下のコマンドを実行してHardhatに内蔵されているEthereumの開発用ノードを起動します。
```bash
$ npx hardhat node
```

4.2 **トークンのデプロイ:**

別のターミナルで以下のコマンドを実行して、MyToken Contractを開発サーバにデプロイします。
```bash
$ npx hardhat run scripts/deploy.js --network localhost
```

### 5. **トークンバランスの確認:**

開発ノードに`MyToken`がデプロイされました。実際にデプロイされたContractを操作してみます。
まずは、`MyToken`のデプロイアカウントである`Account #0`に初期のTokenが発行されていることを確認します。

以下のコマンドを実行してhardhat consoleを起動します。
```bash
$ npx hardhat console
```

以降は、hardhat console上で実行する手順を示しています。

5.1 **バランスの確認:**
```bash
> let MyToken = await ethers.getContractFactory("MyToken");
> let myToken = await MyToken.attach("<contract_address>");
> let balance = await myToken.balanceOf("<account_address>");
> balance.toString();
```
`<contract_address>`には、デプロイされたコントラクトのアドレスを指定します。
`<account_address>`には、`Account #0`のアドレスを指定します。

### 6. **トークンの送金とバランスの確認:**

ここではMyTokenを別のアカウントに送金してみます。Hardhatの開発ノードは初期状態で20個のアカウントが自動生成されます。

最初は0番目のアカウントのみが`MyToken`を持っているので、1番目のアカウントにも送金します。

6.1 **トークンの送金:**
```bash
> await myToken.transfer("<recipient_address>", 1000);
```
`<recipient_address>`には、`Account #1`のアドレスを指定します。

6.2 **バランスの確認:**

以下で実際に1番目のアカウントに`MyToken`が送金されたか確認します。
0番目のアカウントの`MyToken`残高が減少し、1番目のアカウントの残高が増えていることを確認してください。

```bash
> let balance0 = await myToken.balanceOf("<account_address>");
> let balance1 = await myToken.balanceOf("<recipient_address>");
> balance0.toString();
> balance1.toString();
```
`<account_address>`には、`Account #0`のアドレスを指定します。
`<recipient_address>`には、`Account #1`のアドレスを指定します。

これらの手順に従うことで、HardhatとOpenZeppelinを使用してERC20トークンを作成し、ローカルの開発ノードにデプロイし、トークンの送金とバランスの確認を行うことができます。

next&gt;&gt; [2.SepoliaテストネットにContractをデプロイ](./2_DeploySepolia.md)

