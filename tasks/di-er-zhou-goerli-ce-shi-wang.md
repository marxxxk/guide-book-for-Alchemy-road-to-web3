---
description: 《How to Build "Buy Me a Coffee" DeFi dapp》
cover: ../.gitbook/assets/task-cover.jpg
coverY: 0
---

# 第二周（Goerli测试网）

{% hint style="warning" %}
<mark style="color:red;">**特别声明：**</mark> 每周任务前，请参看 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) ，无论此周任务需要用到多少！！！
{% endhint %}

### 一、准备工作

#### &#x20;       1. 完成 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) 中的第4、6步骤，项目命名为 road-to-web3-week2（可以自定义）

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 在 [任务中所用平台和工具](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 的流程中，拿到Alchemy上Goerli测试网App的 <mark style="color:red;">HTTPS、API KEY</mark> 以及<mark style="color:red;">Metamask的钱包私钥</mark>

#### &#x20;       3. 任务中创建的2个目录，BuyABCACoffee-contracts和buyabcacoffee-dapp，是 <mark style="color:red;">同一级</mark> 的，而不能变成一个是另一个的子目录，否则会出现编译或者运行错误，切记\~



### 二、智能合约代码初始化

{% hint style="danger" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**
{% endhint %}

#### &#x20;       1. 点击右边Shell，输入：npm init -y ， 回车(中间选择直接回车)

<figure><img src="../.gitbook/assets/image (69) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 输入：mkdir BuyABCACoffee-contracts，回车

<figure><img src="../.gitbook/assets/image (29) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 输入：cd BuyABCACoffee-contracts ，回车

<figure><img src="../.gitbook/assets/image (49) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 输入： npm init -y ，回车

<figure><img src="../.gitbook/assets/image (1) (1) (4).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 输入： npx hardhat ， 回车(中间选择直接回车，有好几个)

<figure><img src="../.gitbook/assets/image (54) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (62) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_会出现不少 <mark style="color:yellow;">WARN</mark> ，不用管。除非出现 <mark style="color:red;">ERR</mark> 或 <mark style="color:red;">ERROR</mark> 等红色提示，才表示安装出错_
{% endhint %}

#### &#x20;       6. 输入： npm install --save-dev @nomiclabs/hardhat-waffle ， 回车

<figure><img src="../.gitbook/assets/image (36) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       7. 输入： npm install dotenv ， 回车

<figure><img src="../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       8. 左边栏点开contracts目录，把Lock.sol改名成BuyABCACoffee.sol

{% hint style="info" %}
_注意：文件名需与前面创建的目录前缀一致_
{% endhint %}

&#x20;                                                 ![](<../.gitbook/assets/image (46).png>)

&#x20;                                                 ![](<../.gitbook/assets/image (50) (1).png>)

&#x20;                                                ![](<../.gitbook/assets/image (60).png>)

#### &#x20;       9. 改名后回车，自动到中间内容，替换成以下内容

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

// Import this file to use console.log
import "hardhat/console.sol";

// Switch this to your own contract address once deployed, for bookkeeping!
// Example Contract Address on Goerli: 0xDBa03676a2fBb6711CB652beF5B7416A53c1421D

contract BuyABCACoffee {
    // Event to emit when a Memo is created.
    event NewMemo(
        address indexed from,
        uint256 timestamp,
        string name,
        string message
    );
    
    // Memo struct.
    struct Memo {
        address from;
        uint256 timestamp;
        string name;
        string message;
    }
    
    // Address of contract deployer. Marked payable so that
    // we can withdraw to this address later.
    address payable owner;

    // List of all memos received from coffee purchases.
    Memo[] memos;

    constructor() {
        // Store the address of the deployer as a payable address.
        // When we withdraw funds, we'll withdraw here.
        owner = payable(msg.sender);
    }

    /**
     * @dev fetches all stored memos
     */
    function getMemos() public view returns (Memo[] memory) {
        return memos;
    }

    /**
     * @dev buy a coffee for owner (sends an ETH tip and leaves a memo)
     * @param _name name of the coffee purchaser
     * @param _message a nice message from the purchaser
     */
    function buyCoffee(string memory _name, string memory _message) public payable {
        // Must accept more than 0 ETH for a coffee.
        require(msg.value > 0, "can't buy coffee for free!");

        // Add the memo to storage!
        memos.push(Memo(
            msg.sender,
            block.timestamp,
            _name,
            _message
        ));

        // Emit a NewMemo event with details about the memo.
        emit NewMemo(
            msg.sender,
            block.timestamp,
            _name,
            _message
        );
    }

    /**
     * @dev send the entire balance stored in this contract to the owner
     */
    function withdrawTips() public {
        require(owner.send(address(this).balance));
    }
}
```

#### &#x20;       10. 点击scripts目录鼠标右键或者左键点击3个点处，选“Add file”，输入buycoffee.js，流程如图

&#x20;                                                       ![](<../.gitbook/assets/image (73) (1).png>)

&#x20;                                                      ![](<../.gitbook/assets/image (58) (1).png>)

#### &#x20;       11. 输入后回车，自动到内容，复制以下内容

```javascript
const hre = require("hardhat");

// Returns the Ether balance of a given address.
async function getBalance(address) {
  const balanceBigInt = await hre.ethers.provider.getBalance(address);
  return hre.ethers.utils.formatEther(balanceBigInt);
}

// Logs the Ether balances for a list of addresses.
async function printBalances(addresses) {
  let idx = 0;
  for (const address of addresses) {
    console.log(`Address ${idx} balance: `, await getBalance(address));
    idx ++;
  }
}

// Logs the memos stored on-chain from coffee purchases.
async function printMemos(memos) {
  for (const memo of memos) {
    const timestamp = memo.timestamp;
    const tipper = memo.name;
    const tipperAddress = memo.from;
    const message = memo.message;
    console.log(`At ${timestamp}, ${tipper} (${tipperAddress}) said: "${message}"`);
  }
}

async function main() {
  // Get the example accounts we'll be working with.
  const [owner, tipper, tipper2, tipper3] = await hre.ethers.getSigners();

  // We get the contract to deploy.
  const BuyABCACoffee = await hre.ethers.getContractFactory("BuyABCACoffee");
  const buyABCACoffee = await BuyABCACoffee.deploy();

  // Deploy the contract.
  await buyABCACoffee.deployed();
  console.log("BuyABCACoffee deployed to:", buyABCACoffee.address);

  // Check balances before the coffee purchase.
  const addresses = [owner.address, tipper.address, buyABCACoffee.address];
  console.log("== start ==");
  await printBalances(addresses);

  // Buy the owner a few coffees.
  const tip = {value: hre.ethers.utils.parseEther("1")};
  await buyABCACoffee.connect(tipper).buyCoffee("Carolina", "You're the best!", tip);
  await buyABCACoffee.connect(tipper2).buyCoffee("Vitto", "Amazing teacher", tip);
  await buyABCACoffee.connect(tipper3).buyCoffee("Kay", "I love my Proof of Knowledge", tip);

  // Check balances after the coffee purchase.
  console.log("== bought coffee ==");
  await printBalances(addresses);

  // Withdraw.
  await buyABCACoffee.connect(owner).withdrawTips();

  // Check balances after withdrawal.
  console.log("== withdrawTips ==");
  await printBalances(addresses);

  // Check out the memos.
  console.log("== memos ==");
  const memos = await buyABCACoffee.getMemos();
  printMemos(memos);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

#### &#x20;       12. 点击deploy.js文件，自动到内容，复制以下内容替换掉

```javascript
// scripts/deploy.js

const hre = require("hardhat");

async function main() {
  // We get the contract to deploy.
  const BuyABCACoffee = await hre.ethers.getContractFactory("BuyABCACoffee");
  const buyABCACoffee = await BuyABCACoffee.deploy();

  await buyABCACoffee.deployed();

  console.log("BuyABCACoffee deployed to:", buyABCACoffee.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

#### &#x20;       13. 点击scripts目录鼠标右键或者左键点击3个点处，选“Add file”，输入withdraw.js，流程如图

&#x20;                                             ![](<../.gitbook/assets/image (6) (2).png>)

&#x20;                                             ![](<../.gitbook/assets/image (37) (2).png>)

#### &#x20;       14. 回车后，跳到内容，复制以下进去（记得保存）

```javascript
// scripts/withdraw.js

const hre = require("hardhat");
const abi = require("../artifacts/contracts/BuyABCACoffee.sol/BuyABCACoffee.json");

async function getBalance(provider, address) {
  const balanceBigInt = await provider.getBalance(address);
  return hre.ethers.utils.formatEther(balanceBigInt);
}

async function main() {
  // Get the contract that has been deployed to Goerli.
  const contractAddress="0x314Cb061D81759F339c6F026c86D09Ad528A31b5";
  const contractABI = abi.abi;

  // Get the node connection and wallet connection.
  const provider = new hre.ethers.providers.AlchemyProvider("goerli", process.env.GOERLI_API_KEY);

  // Ensure that signer is the SAME address as the original contract deployer,
  // or else this script will fail with an error.
  const signer = new hre.ethers.Wallet(process.env.PRIVATE_KEY, provider);

  // Instantiate connected contract.
  const buyABCACoffee = new hre.ethers.Contract(contractAddress, contractABI, signer);

  // Check starting balances.
  console.log("current balance of owner: ", await getBalance(provider, signer.address), "ETH");
  const contractBalance = await getBalance(provider, buyABCACoffee.address);
  console.log("current balance of contract: ", await getBalance(provider, buyABCACoffee.address), "ETH");

  // Withdraw funds if there are funds to withdraw.
  if (contractBalance !== "0.0") {
    console.log("withdrawing funds..")
    const withdrawTxn = await buyABCACoffee.withdrawTips();
    await withdrawTxn.wait();
  } else {
    console.log("no funds to withdraw!");
  }

  // Check ending balance.
  console.log("current balance of owner: ", await getBalance(provider, signer.address), "ETH");
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

#### &#x20;       15. 点击hardhat.config.js文件，自动跳到内容，复制以下内容进去（记得保存）

```javascript
// hardhat.config.js

require("@nomiclabs/hardhat-ethers");
require("@nomiclabs/hardhat-waffle");
require('dotenv').config();

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

const GOERLI = process.env.GOERLI_URL;
const PRIVATE = process.env.PRIVATE_KEY;
//console.log(GOERLI);

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.9",
  networks: {
    goerli: {
      url: GOERLI,
      accounts: [PRIVATE]
    }
  }
};
```

#### &#x20;       16. 点击目录BuyABCACoffee-contracts右边三个点，选“Add file”，输入 <mark style="color:red;">.env</mark>

{% hint style="info" %}
_**注意:**  .env此文件最前面有小数点_
{% endhint %}

&#x20;                                          ![](<../.gitbook/assets/image (23) (2).png>)

&#x20;                                          ![](<../.gitbook/assets/image (19) (2).png>)

#### &#x20;       17. 回车后，跳到内容，复制以下内容进去（记得保存）

```
GOERLI_URL=https://eth-goerli.alchemyapi.io/v2/<your api key>
GOERLI_API_KEY=<your api key>
PRIVATE_KEY=<your metamask api key>
```

{% hint style="info" %}
_将上面3项依次替换成前面_ [_准备工作_](di-er-zhou-goerli-ce-shi-wang.md#yi-zhun-bei-gong-zuo) _中的_ _<mark style="color:red;">HTTPS 、API KEY和钱包私钥</mark>_
{% endhint %}



### 三、部署智能合约

{% hint style="danger" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**
{% endhint %}

#### &#x20;       1. 点击“Shell”，输入： npx hardhat run scripts/buycoffee.js ，回车

<figure><img src="../.gitbook/assets/image (68) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 输入： npx hardhat run scripts/deploy.js ，回车

<figure><img src="../.gitbook/assets/image (25) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 输入： npx hardhat run scripts/deploy.js --network goerli ，回车

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_这时部署到Goerli测试网了，记录一下_ _<mark style="color:red;">deployed to</mark>_ <mark style="color:red;"></mark><mark style="color:red;"></mark> _后面的地址，一会就用到_
{% endhint %}

{% hint style="warning" %}
注意： 若部署遇到以下错误，则表示gas费不够，需要去领水

&#x20;            ![](<../.gitbook/assets/image (41).png>)
{% endhint %}

#### &#x20;        4. 输入： npx hardhat run scripts/withdraw.js ， 回车

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_中间的 <mark style="color:red;">0.004 ETH</mark> 是不固定的，有些会出现 0.000_
{% endhint %}

#### &#x20;       5. 删除.env（<mark style="color:red;">此步非常重要！！！</mark>）

&#x20;                                             ![](<../.gitbook/assets/image (26) (3).png>)



### 四、Dapp代码初始化

{% hint style="danger" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**
{% endhint %}

#### &#x20;       1. 点击Shell，需回到road-to-web3-week2目录，输入： cd .. , 回车 （若当前目录已是road-to-web3-week2，则跳过

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 输入： npx create-next-app buyabcacoffee-dapp ， 回车(中间选择直接回车)

{% hint style="info" %}
_命令中最后的字符串是 目录名_

_此目录名可以自命名，后续需保持与此一致。_

_创建的目录可能需要等一小段时间才会在左边栏显示出来_
{% endhint %}

<figure><img src="../.gitbook/assets/image (66) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (64) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 进入目录，输入： cd buyabcacoffee-dapp ，回车

<figure><img src="../.gitbook/assets/image (52) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 输入： npm install --save ethers ， 回车

<figure><img src="../.gitbook/assets/image (24) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 左边栏点开BuyABCACoffee-contracts目录，选择artifacts/contract/BuyABCACoffee.sol/BuyABCACoffee.json文件，点其右边的三个点，选“rename“，复制其文件名

&#x20;                                      ![](<../.gitbook/assets/image (71).png>)

#### &#x20;       6. 点击buyabcacoffee-dapp目录右边的三个点，选择“Add folder“，输入utils，回车

&#x20;                                        ![](<../.gitbook/assets/image (59) (1).png>)

&#x20;                                    ![](<../.gitbook/assets/image (56).png>)

#### &#x20;       7. 点击utils右边三个点，选择“Add file“，将第5步复制的文件名粘贴，并加上.json后缀，回车

&#x20;                                    ![](<../.gitbook/assets/image (72) (1).png>)

&#x20;                                 ![](<../.gitbook/assets/image (65) (1).png>)

#### &#x20;       8. 将第5步中 <mark style="color:red;">BuyABCACoffee.json</mark> 的内容复制到 utils下面的同名文件中

<figure><img src="../.gitbook/assets/image (31) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       9. 点开 pages 目录，点击 \_app.js文件右边3点，选rename，改成 <mark style="color:red;">\_app.jsx</mark>

<figure><img src="../.gitbook/assets/image (11) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (33) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       10. 点击 index.js 文件右边3点，选rename，改成 <mark style="color:red;">index.jsx</mark>

<figure><img src="../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       11. 点击 index.jsx，跳到内容，复制以下内容替换(记得保存)

{% hint style="info" %}
_**注意：** 下面代码 <mark style="color:red;">const contractAddress =</mark>  处后面需修改为_ [_<mark style="color:red;">部署智能合约</mark>_](di-er-zhou-goerli-ce-shi-wang.md#san-bu-shu-zhi-neng-he-yue) _<mark style="color:red;"></mark> 中的第3步复制的合约地址_
{% endhint %}

```javascript
import abi from '../utils/BuyABCACoffee.json';
import { ethers } from "ethers";
import Head from 'next/head'
import Image from 'next/image'
import React, { useEffect, useState } from "react";
import styles from '../styles/Home.module.css'

export default function Home() {
  // Contract Address & ABI
  const contractAddress = "0xf499546186d98542d74cc9abb74e3fe1154886d0";
  const contractABI = abi.abi;

  // Component state
  const [currentAccount, setCurrentAccount] = useState("");
  const [name, setName] = useState("");
  const [message, setMessage] = useState("");
  const [memos, setMemos] = useState([]);

  const onNameChange = (event) => {
    setName(event.target.value);
  }

  const onMessageChange = (event) => {
    setMessage(event.target.value);
  }

  // Wallet connection logic
  const isWalletConnected = async () => {
    try {
      const { ethereum } = window;

      const accounts = await ethereum.request({ method: 'eth_accounts' })
      console.log("accounts: ", accounts);

      if (accounts.length > 0) {
        const account = accounts[0];
        console.log("wallet is connected! " + account);
      } else {
        console.log("make sure MetaMask is connected");
      }
    } catch (error) {
      console.log("error: ", error);
    }
  }

  const connectWallet = async () => {
    try {
      const { ethereum } = window;

      if (!ethereum) {
        console.log("please install MetaMask");
      }

      const accounts = await ethereum.request({
        method: 'eth_requestAccounts'
      });

      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.log(error);
    }
  }

  const buyCoffee = async () => {
    try {
      const { ethereum } = window;

      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum, "any");
        const signer = provider.getSigner();
        const buyMeACoffee = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        );

        console.log("buying coffee..")
        const coffeeTxn = await buyMeACoffee.buyCoffee(
          name ? name : "honey",
          message ? message : "Enjoy your coffee!",
          { value: ethers.utils.parseEther("0.001") }
        );

        await coffeeTxn.wait();

        console.log("mined ", coffeeTxn.hash);

        console.log("coffee purchased!");

        // Clear the form fields.
        setName("");
        setMessage("");
      }
    } catch (error) {
      console.log(error);
    }
  };

  // Function to fetch all memos stored on-chain.
  const getMemos = async () => {
    try {
      const { ethereum } = window;
      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const buyMeACoffee = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        );

        console.log("fetching memos from the blockchain..");
        const memos = await buyMeACoffee.getMemos();
        console.log("fetched!");
        setMemos(memos);
      } else {
        console.log("Metamask is not connected");
      }

    } catch (error) {
      console.log(error);
    }
  };

  useEffect(() => {
    let buyMeACoffee;
    isWalletConnected();
    getMemos();

    // Create an event handler function for when someone sends
    // us a new memo.
    const onNewMemo = (from, timestamp, name, message) => {
      console.log("Memo received: ", from, timestamp, name, message);
      setMemos((prevState) => [
        ...prevState,
        {
          address: from,
          timestamp: new Date(timestamp * 1000),
          message,
          name
        }
      ]);
    };

    const { ethereum } = window;

    // Listen for new memo events.
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum, "any");
      const signer = provider.getSigner();
      buyMeACoffee = new ethers.Contract(
        contractAddress,
        contractABI,
        signer
      );

      buyMeACoffee.on("NewMemo", onNewMemo);
    }

    return () => {
      if (buyMeACoffee) {
        buyMeACoffee.off("NewMemo", onNewMemo);
      }
    }
  }, []);

  return (
    <div>
      <Head>
        <title>BuyCoffee Dapp</title>
        <meta name="description" content="Tipping site" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main className={styles.main}>
        <div className={styles.nav}>
          <h1 className={styles.title}>
            Buy Soulmate a Coffee!
          </h1>
        </div>

        <div className={styles.front}>
            <div className={styles.fronttitle}>
                <Image src="/backimg1.jpg" width={80} height={80} className={styles.images}></Image>
                <h2>FRONT TABLE</h2>
            </div>
            <div className={styles.frontcontent}>
                {currentAccount ? (
                    <form className={styles.form}>
                        <div className={styles.formusername}>
                            <span>Name: </span>
                            <input id="name" type="text" placeholder="honey" onChange={onNameChange} />
                        </div>

                        <div>
                            <span>Message: </span>
                            <textarea rows={3} placeholder="Enjoy your coffee!" id="message" onChange={onMessageChange} required>
                            </textarea>
                        </div>

                        <div>
                            <button type="button" onClick={buyCoffee}>Send 1 Coffee for 0.001ETH</button>
                        </div>
                    </form>
                ) : (
                    <button className={styles.connectbutton} onClick={connectWallet}> Connect your wallet </button>
                )}
            </div>
        </div>

        <div className={styles.showMemos}>
            {currentAccount && (
                <div className={styles.Memostitle}>
                    <Image src="/memo.png" width={80} height={80} className={styles.images}></Image>
                    <h2>Memos received</h2>
                </div>
            )}

            {currentAccount && (<div className={styles.Memoscontent}>
                {currentAccount && (memos.map(
                    (memo, idx) => {
                        return (      
                                <div key={idx} className={styles.Memocss}>
                                    <p style={{ "fontWeight": "bold" }}>`{memo.message}`</p>
                                    <p>From: {memo.name} at {memo.timestamp.toString()}</p>
                                </div>
                        )
                    })
                )}
            </div>)}
        </div>
      </main>

      <footer className={styles.footer}>
        <a href="https://alchemy.com/?a=roadtoweb3weektwo" target="_blank" rel="noopener noreferrer">
          Created by @FinderTechnical for Alchemy's Road to Web3 lesson two!
        </a>
      </footer>
    </div>
  )
}
```

#### &#x20;       12. 点开 styles 目录，点击 globals.css 文件，复制以下内容替换（记得保存）

```css
html,
body {
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  background-image: url(../public/backimg2.jpg);
  background-size:100% 100%;
}

a {
  color: inherit;
  text-decoration: none;
}

* {
  box-sizing: border-box;
}

@media (prefers-color-scheme: dark) {
  html {
    color-scheme: dark;
  }
  body {
    color: white;
    background: black;
  }
}
```

#### &#x20;        13. 点击 Home.module.css 文件，复制以下内容替换（记得保存）

```css
.main {
  min-height: 90vh;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  gap: 3rem;
}

.nav {
  height:80px;    
  margin-bottom:5px;    
  text-align: center;
  font: 18px/30px 微软雅黑;
  display: flex;
  column-gap: 2rem;
  align-items: center;
  justify-content: center;
}

.front {
  display: flex;
  flex-direction: column;
  gap:1rem;
  justify-content: flex-start;
  align-items: center;
  margin-left: 4rem;
  width: 30%;
}

.fronttitle {
  display: flex;
  justify-content: center;
  align-items: center;
  color: darkturquoise;
  column-gap: 2rem;
}

.frontcontent {
  padding: 0 2rem;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  background: #3EB1DD;
  width: fit-content;
  border-radius: 15px;
}

.form div {
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  margin: 10px;
  gap:1rem;
}

.form input[type="text"] {
  color: #888;
  width: 60%;
  border: 1px solid #C5E2FF;
  border-radius: 8px;
  background: #FBFBFB;
  outline: 0;
  -webkit-box-shadow:inset 0px 1px 6px #ECF3F5;
  box-shadow: inset 0px 1px 6px #ECF3F5;
  font: 200 12px/25px Arial, Helvetica, sans-serif;
  height: 30px;
  line-height:15px;
}

.form textarea {
  color: #888;
  padding: 5px 0px 0px 5px;
  width: 60%;
  border: 1px solid #C5E2FF;
  border-radius: 8px;
  background: #FBFBFB;
  outline: 0;
  -webkit-box-shadow:inset 0px 1px 6px #ECF3F5;
  box-shadow: inset 0px 1px 6px #ECF3F5;
  font: 200 12px/25px Arial, Helvetica, sans-serif;
}

.form button {
  padding: 10px 30px 10px 30px;
  background: #66C1E4;
  border: none;
  color: #FFF;
  box-shadow: 1px 1px 1px #4C6E91;
  -webkit-box-shadow: 1px 1px 1px #4C6E91;
  -moz-box-shadow: 1px 1px 1px #4C6E91;
  text-shadow: 1px 1px 1px #5079A3;
  border-radius: 9999px;
  cursor: pointer;
}

.form button:hover {
  background-color: #b9e769;
  -webkit-box-shadow: 10px 10px 99px 6px rgba(185, 231, 105, 1);
  -moz-box-shadow: 10px 10px 99px 6px rgba(185, 231, 105, 1);
  box-shadow: 10px 10px 99px 6px rgba(185, 231, 105, 1);
}

.form div>span {
  float: left;
  width: 30%;
  text-align: right;
  padding-right: 15px;
  font-weight: bold;
  color: black;
  letter-spacing: 2px;
  text-shadow: 1px 1px 1px #000, -1px -1px 1px #fff;
}

.showMemos {
  display: flex;
  flex-direction: column;
  gap:1rem;
  justify-content: flex-start;
  align-items: center;
  
  width: 40%;
}

.Memostitle {
  display: flex;
  justify-content: center;
  align-items: center;
  color: snow;
  column-gap: 2rem;
}

.Memoscontent {
  display: flex;
  justify-content: stretch;
  flex-direction: row-reverse;
  text-align: center;
  white-space: nowrap;
  overflow-x: scroll;
  overflow-y: hidden;
  width: 70%;
}

.Memocss {
  border-radius: 10px;
  padding: 5px;
  margin: 5px;
  background-position: 0px 0px, 10px 10px;
  background-size: 8px 8px;
  background-image: linear-gradient(
      45deg,
      #eee 25%,
      transparent 25%,
      transparent 75%,
      #eee 75%,
      #eee 100%
    ),
    linear-gradient(45deg, #eee 25%, white 25%, white 75%, #eee 75%, #eee 100%);
    opacity:0.7;
}

.footer {
  display: flex;
  flex: 1;
  padding: 2rem 0;
  border-top: 1px solid #eaeaea;
  justify-content: center;
  align-items: center;
}

.footer a {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-grow: 1;
}

.title a {
  color: #0070f3;
  text-decoration: none;
}

.title a:hover,
.title a:focus,
.title a:active {
  text-decoration: underline;
}

.title {
  margin: 0;
  line-height: 1.15;
  font-size: 4rem;
  color: darkcyan;
  letter-spacing: 2px;
  font-style: italic;
  text-shadow: 1px 1px 1px #000, -1px -1px 1px #fff;
}

.title,
.description {
  text-align: center;
}

.description {
  margin: 4rem 0;
  line-height: 1.5;
  font-size: 1.5rem;
}

.connectbutton {
  background-color: #4CAF50; /* Green */
  border: none;
  border-radius: 20px;
  color: white;
  padding: 16px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  -webkit-transition-duration: 0.4s; /* Safari */
  transition-duration: 0.4s;
  cursor: pointer;
  width: inherit;
}

.images {
  border-radius: 50%;
}
```

#### &#x20;       14. 上传需要的图片

&#x20;           1\) 下面3张图片用 <mark style="color:red;">右键->图片另存为</mark> 到本地

&#x20;               背景图片，下载后重命名为 <mark style="color:red;">backimg2.jpg</mark>

<figure><img src="../.gitbook/assets/image (40) (2).png" alt=""><figcaption></figcaption></figure>

&#x20;               <mark style="color:red;"></mark>                图片1，下载后重命名为 <mark style="color:red;">backimg1.jpg</mark>&#x20;

&#x20;                                                ![](<../.gitbook/assets/image (70).png>)

&#x20;               图片2，下载后重命名为 <mark style="color:red;">memo.png</mark>&#x20;

&#x20;                                                ![](<../.gitbook/assets/image (22) (2).png>)

{% hint style="info" %}
_也可以使用自己的图片，只是文件名一定要改成上面3个。_

_若懂代码，可直接修改代码中的图片名。_

_图片可去_ [_https://www.pexels.com/zh-cn/_](https://www.pexels.com/zh-cn/) _上寻找_
{% endhint %}

&#x20;           2）左边栏点击Files右边3点，选 Upload file

<figure><img src="../.gitbook/assets/image (9) (3).png" alt=""><figcaption></figcaption></figure>

&#x20;            3）找到刚才下的三张图片，进行上传

&#x20;                                                ![](<../.gitbook/assets/image (51) (1).png>)

&#x20;           4）上传后，将其拖动到buyabcacoffee-dapp/public目录下

&#x20;                  一开始在这里，

&#x20;                                                ![](<../.gitbook/assets/image (42) (2).png>)

&#x20;                     然后

&#x20;                                                ![](<../.gitbook/assets/image (7) (2).png>)



### 五、运行测试Dapp

{% hint style="info" %}
_运行前，多检查代码文件内容，有时会出现内容消失或者恢复到复制前的情况，需再次跟随之前章节的流程进行拷贝_
{% endhint %}

#### &#x20;       1. 点击左边栏最上面Files右边的三个点，选择“show hidden files“,会看到一个叫.replit的文件

<figure><img src="../.gitbook/assets/image (17) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 点击.replit，跳到内容，复制以下内容进去（记得保存）

```
run = "npm --prefix ./buyabcacoffee-dapp run dev"
entrypoint = './buyabcacoffee-dapp/pages/index.jsx'
```

#### &#x20;       3. 点击最上面中间的RUN，运行Dapp，等待一段时间即会出现页面示意

<figure><img src="../.gitbook/assets/image (47) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           1）然后显示出下面即成功（console的提示可能会更多一些，只要不是error，都正常）

<figure><img src="../.gitbook/assets/image (10) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 复制上图中的 https 链接，新开一个网页打开它

<figure><img src="../.gitbook/assets/image (61) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           1）点击 Connect your wallet ，选择钱包进行连接，连接好后

<figure><img src="../.gitbook/assets/image (30) (2).png" alt=""><figcaption></figcaption></figure>

&#x20;           2）输入 名字，以及想发送的话，点击 Send 1 Coffee for 0.001 ETH，确认gas

<figure><img src="../.gitbook/assets/image (27) (2).png" alt=""><figcaption></figcaption></figure>

&#x20;            3）等待合约交互，可从metamask的活动里查看

&#x20;                                           ![](<../.gitbook/assets/image (67) (1) (1).png>)

&#x20;            4）成功后，活动成功，memo也会展示出最新信息

&#x20;                                            ![](<../.gitbook/assets/image (2) (1) (1).png>)

&#x20;                                            ![](<../.gitbook/assets/image (13) (1) (1).png>)

#### &#x20;       5. 从Metamask上进入区块浏览器，可查看最新合约的相关信息

&#x20;                                              ![](<../.gitbook/assets/image (66) (1).png>)

<figure><img src="../.gitbook/assets/image (48) (1).png" alt=""><figcaption></figcaption></figure>



### 六、上传到Github

#### &#x20;       参照 [<mark style="color:red;">任务中所用平台和工具</mark>](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 中 replit 如何提交到Github的操作流程

{% hint style="info" %}
_切记删除与私钥相关的东西_
{% endhint %}



### 七、提交

#### &#x20;       本周能证明完成的地址：

&#x20;            1）合约地址：[https://goerli.etherscan.io/address/](https://goerli.etherscan.io/address/)你的合约地址

&#x20;            2）replit上运行成功的Dapp地址（后缀为repl.co）

&#x20;            3）github地址

{% hint style="info" %}
_<mark style="color:red;">合约地址</mark> 在第三部分查看，<mark style="color:red;">Dapp地址</mark> 在第五部分查看，点_ [_<mark style="color:red;">此处</mark>_](../preparation/ren-wu-ti-jiao-ren-wu-wan-cheng-shi-kan.md) _到任务提交部分_

_（若找不到合约地址，请参看_ [_<mark style="color:red;">任务中所用平台和工具</mark> _ ](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md)_中钱包相关部分的如何寻找合约地址）_
{% endhint %}

