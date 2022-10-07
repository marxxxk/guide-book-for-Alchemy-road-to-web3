---
description: 《How to build a betting game on Optimism》
cover: ../.gitbook/assets/task-cover.jpg
coverY: 0
---

# 第八周（Optimism Goerli测试网）

{% hint style="warning" %}
<mark style="color:red;">**特别声明：**</mark> 每周任务前，请参看 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) ，无论此周任务需要用到多少！！！
{% endhint %}

### 一、准备工作

#### &#x20;       1. 完成 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) 中的第4、6步骤，项目命名为 road-to-web3-week8（可以自定义）

#### &#x20;       2. 在 [任务中所用平台和工具](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 的流程中，Alchemy上创建Optimism Goerli测试网App，记录下其 <mark style="color:red;">HTTPS</mark> 以及 <mark style="color:red;">Metamask的钱包私钥</mark>

#### &#x20;       3. 在 [任务中所用平台和工具](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 的流程中，在Metamask上添加 Optimism Goerli 网络，url会用到2步骤中的 HTTPS



### 二、项目代码初始化

{% hint style="warning" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**                                             &#x20;
{% endhint %}

#### &#x20;       1. 右边点Shell，输入 mkdir Casino ，回车；输入 cd Casino，回车

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 输入 npm install hardhat，回车（中间选择时直接回车）

<figure><img src="../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 输入 npm install --save-dev @nomiclabs/hardhat-waffle ，回车

<figure><img src="../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 输入 npx hardhat ，回车（中间几次选择都直接回车）

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 输入 npm install dotenv ，回车

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       6. 左边栏点开Casino/contracts目录，把Lock.sol改名为Casino.sol

&#x20;                                             ![](<../.gitbook/assets/image (92).png>)

&#x20;                                             ![](<../.gitbook/assets/image (23).png>)

&#x20;                                            ![](../.gitbook/assets/image.png)

#### &#x20;        7. 回车后，自动跳到内容，复制以下内容替换（记得保存）

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

contract Casino {

  struct ProposedBet {
    address sideA;
    uint value;
    uint placedAt;
    bool accepted;   
  }    // struct ProposedBet


  struct AcceptedBet {
    address sideB;
    uint acceptedAt;
    uint randomB;
  }   // struct AcceptedBet

  // Proposed bets, keyed by the commitment value
  mapping(uint => ProposedBet) public proposedBet;

  // Accepted bets, also keyed by commitment value
  mapping(uint => AcceptedBet) public acceptedBet;

  event BetProposed (
    uint indexed _commitment,
    uint value
  );

  event BetAccepted (
    uint indexed _commitment,
    address indexed _sideA
  );


  event BetSettled (
    uint indexed _commitment,
    address winner,
    address loser,
    uint value    
  );


  // Called by sideA to start the process
  function proposeBet(uint _commitment) external payable {
    require(proposedBet[_commitment].value == 0,
      "there is already a bet on that commitment");
    require(msg.value > 0,
      "you need to actually bet something");

    proposedBet[_commitment].sideA = msg.sender;
    proposedBet[_commitment].value = msg.value;
    proposedBet[_commitment].placedAt = block.timestamp;
    // accepted is false by default

    emit BetProposed(_commitment, msg.value);
  }  // function proposeBet


  // Called by sideB to continue
  function acceptBet(uint _commitment, uint _random) external payable {

    require(!proposedBet[_commitment].accepted,
      "Bet has already been accepted");
    require(proposedBet[_commitment].sideA != address(0),
      "Nobody made that bet");
    require(msg.value == proposedBet[_commitment].value,
      "Need to bet the same amount as sideA");

    acceptedBet[_commitment].sideB = msg.sender;
    acceptedBet[_commitment].acceptedAt = block.timestamp;
    acceptedBet[_commitment].randomB = _random;
    proposedBet[_commitment].accepted = true;

    emit BetAccepted(_commitment, proposedBet[_commitment].sideA);
  }   // function acceptBet


  // Called by sideA to reveal their random value and conclude the bet
  function reveal(uint _random) external {
    uint _commitment = uint256(keccak256(abi.encodePacked(_random)));
    address payable _sideA = payable(msg.sender);
    address payable _sideB = payable(acceptedBet[_commitment].sideB);
    uint _agreedRandom = _random ^ acceptedBet[_commitment].randomB;
    uint _value = proposedBet[_commitment].value;

    require(proposedBet[_commitment].sideA == msg.sender,
      "Not a bet you placed or wrong value");
    require(proposedBet[_commitment].accepted,
      "Bet has not been accepted yet");

    // Pay and emit an event
    if (_agreedRandom % 2 == 0) {
      // sideA wins
      _sideA.transfer(2*_value);
      emit BetSettled(_commitment, _sideA, _sideB, _value);
    } else {
      // sideB wins
      _sideB.transfer(2*_value);
      emit BetSettled(_commitment, _sideB, _sideA, _value);      
    }

    // Cleanup
    delete proposedBet[_commitment];
    delete acceptedBet[_commitment];

  }  // function reveal

}   // contract Casino
```

#### &#x20;       8. 点开Casino/test目录，修改Lock.js名字为casino-test.js，方法和步骤7类似

&#x20;                                        ![](<../.gitbook/assets/image (89).png>)

#### &#x20;       9. 回车后，自动跳到内容，复制以下替换（记得保存）

```javascript
const chai = require("chai")
const expect = chai.expect
chai.use(require('chai-as-promised'))

const valA = ethers.utils.keccak256(0xBAD060A7)
const hashA = ethers.utils.keccak256(valA)
const valBwin = ethers.utils.keccak256(0x600D60A7)
const valBlose = ethers.utils.keccak256(0xBAD060A7)

// Chai's expect(<operation>).to.be.revertedWith behaves
// strangely, so I'm implementing that functionality myself
// with try/catch
const interpretErr = err => {
  if (err.reason)
    return err.reason
  else
    return err.stackTrace[0].message.value.toString('ascii')
}

describe("Casino", async () => {
  it("Not allow you to propose a zero wei bet", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    try {
      tx = await c.proposeBet(hashA)
      rcpt = await tx.wait()

      // If we get here, it's a fail
      expect("this").to.equal("fail")
    } catch(err) {
      expect(interpretErr(err)).to
        .match(/you need to actually bet something/)
    }
  })   // it "Not allow you to bet zero wei"

  it("Not allow you to accept a bet that doesn't exist", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    try {
      tx = await c.acceptBet(hashA, valBwin, {value: 10})
      rcpt = await tx.wait()
      expect("this").to.equal("fail")
    } catch (err) {
        expect(interpretErr(err)).to
          .match(/Nobody made that bet/)
    }
  })   // it "Not allow you to accept a bet that doesn't exist" 

  it("Allow you to propose and accept bets", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    tx = await c.proposeBet(hashA, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")
    tx = await c.acceptBet(hashA, valBwin, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetAccepted")      
  })   // it "Allow you to accept a bet"

  it("Not allow you to accept an already accepted bet", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    tx = await c.proposeBet(hashA, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")
    tx = await c.acceptBet(hashA, valBwin, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetAccepted")
    try {
      tx = await c.acceptBet(hashA, valBwin, {value: 10})
      rcpt = await tx.wait()   
      expect("this").to.equal("fail")      
    } catch (err) {
        expect(interpretErr(err)).to
          .match(/Bet has already been accepted/)
    }          
  })   // it "Not allow you to accept an already accepted bet" 

  it("Not allow you to accept with the wrong amount", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    tx = await c.proposeBet(hashA, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")
    try {
      tx = await c.acceptBet(hashA, valBwin, {value: 11})
      rcpt = await tx.wait()   
      expect("this").to.equal("fail")      
    } catch (err) {
        expect(interpretErr(err)).to
          .match(/Need to bet the same amount as sideA/)
    }          
  })   // it "Not allow you to accept with the wrong amount"   

  it("Not allow you to reveal with wrong value", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    tx = await c.proposeBet(hashA, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")
    tx = await c.acceptBet(hashA, valBwin, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetAccepted")
    try {
      tx = await c.reveal(valBwin)
      rcpt = await tx.wait()   
      expect("this").to.equal("fail")      
    } catch (err) {
        expect(interpretErr(err)).to
          .match(/Not a bet you placed or wrong value/)
    }          
  })   // it "Not allow you to accept an already accepted bet" 

  it("Not allow you to reveal before bet is accepted", async () => {
    f = await ethers.getContractFactory("Casino")
    c = await f.deploy()

    tx = await c.proposeBet(hashA, {value: 10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")
    try {
      tx = await c.reveal(valA)
      rcpt = await tx.wait()   
      expect("this").to.equal("fail")      
    } catch (err) {
        expect(interpretErr(err)).to
          .match(/Bet has not been accepted yet/)
    }          
  })   // it "Not allow you to reveal before bet is accepted"  

  it("Work all the way through (B wins)", async () => {
    signer = await ethers.getSigners()
    f = await ethers.getContractFactory("Casino")
    cA = await f.deploy()
    cB = cA.connect(signer[1])

    tx = await cA.proposeBet(hashA, {value: 1e10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")

    tx = await cB.acceptBet(hashA, valBwin, {value: 1e10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetAccepted")      

    // A sends the transaction, so the change due to the
    // bet will only be clearly visible in B

    preBalanceB = await ethers.provider.getBalance(signer[1].address)    
    tx = await cA.reveal(valA)
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetSettled")
    postBalanceB = await ethers.provider.getBalance(signer[1].address)        
    deltaB = postBalanceB.sub(preBalanceB)
    expect(deltaB.toNumber()).to.equal(2e10)   


  })   // it "Work all the way through (B wins)"

  it("Work all the way through (A wins)", async () => {
    signer = await ethers.getSigners()
    f = await ethers.getContractFactory("Casino")
    cA = await f.deploy()
    cB = cA.connect(signer[1])

    tx = await cA.proposeBet(hashA, {value: 1e10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetProposed")

    tx = await cB.acceptBet(hashA, valBlose, {value: 1e10})
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetAccepted")      

    // A sends the transaction, so the change due to the
    // bet will only be clearly visible in B
    preBalanceB = await ethers.provider.getBalance(signer[1].address)    
    tx = await cA.reveal(valA)
    rcpt = await tx.wait()
    expect(rcpt.events[0].event).to.equal("BetSettled")
    postBalanceB = await ethers.provider.getBalance(signer[1].address)        
    deltaB = postBalanceB.sub(preBalanceB)
    expect(deltaB.toNumber()).to.equal(0) 
  }) // it "Work all the way through (A wins)"

}) // describe("Casino")
```

#### &#x20;       10. 点击hardhat.config.js，复制以下内容替换（记得保存）

```javascript
require("@nomiclabs/hardhat-waffle");
require('dotenv').config()

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more
const GOERLI_URL = process.env.GOERLI_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

/**
 * 切记私钥不要上传到仓库 切记 切记
 */

module.exports = {
  solidity: "0.8.9",
  networks: {
    
    "optimismgoerli": {
       url: GOERLI_URL,
       accounts: [ PRIVATE_KEY ]
    }
  }
};
```

#### &#x20;       11. 点击Casino右侧的三个点，选Add file，输入 <mark style="color:red;">.env</mark>                                               &#x20;

&#x20;                                          ![](<../.gitbook/assets/image (27).png>)

&#x20;                                      ![](<../.gitbook/assets/image (58).png>)

#### &#x20;       12. 回车后，自动跳到内容，先复制以下进去

```
GOERLI_URL=""
PRIVATE_KEY=""
```

{% hint style="info" %}
_GOERLI\_URL=后面填入_ [_准备工作_](di-ba-zhou-optimism-goerli-ce-shi-wang.md#yi-zhun-bei-gong-zuo) _中的 <mark style="color:red;">HTTPS</mark>_

_PRIVATE\_KEY=后面填入_ [_准备工作_](di-ba-zhou-optimism-goerli-ce-shi-wang.md#yi-zhun-bei-gong-zuo) _中的 <mark style="color:red;">钱包私钥</mark>_
{% endhint %}

&#x20;             填完类似

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       13. 点击最上面Files的右边3个点处，选择“show hidden files”，然后点击.replit文件

&#x20;                                                   ![](<../.gitbook/assets/image (91).png>)

&#x20;                                                      ![](<../.gitbook/assets/image (6).png>)

#### &#x20;       14. 清除.replit全部内容（记得保存）



### 三、测试智能合约的交互

{% hint style="warning" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**

**如果退出到第一层，要再次进入Casino目录，重新运行下面步骤 1**
{% endhint %}

#### &#x20;       1. 点击右边Shell，检查目录，输入 npx hardhat console --network optimismgoerli，回车

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_出现最下面那个小箭头就成功_
{% endhint %}

#### &#x20;       2. 输入 signer = await ethers.getSigner();  ，回车

{% hint style="info" %}
_下图红框处为钱包地址_
{% endhint %}

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_查看下面红框处网络，如果不对，表示钱包没切换到 <mark style="color:red;">optimism goerli</mark> 上_
{% endhint %}

<figure><img src="../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 输入 balance0 = await ethers.provider.getBalance((await ethers.getSigner()).address) ，回车

{% hint style="info" %}
_显示的大数字是当前钱包的币余额多少_
{% endhint %}

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 输入 factory = ethers.getContractFactory("Casino") ，回车

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 输入 factory = await factory ，回车

<figure><img src="../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       6. 部署前，需要确定钱包的测试币是否足够，不够可去 [https://goerlifaucet.com/](https://goerlifaucet.com/) 领取

#### &#x20;       7. 输入 casino = await factory.deploy() ，回车

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_信息的最后可找到合约地址，也可用_ [_任务中所用平台和工具_](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) _中钱包找合约地址方法去对比_
{% endhint %}

#### &#x20;       8. 以下3句，每一句输入后都回车

```
const valA = ethers.utils.keccak256(0xBAD060A7)
const hashA = ethers.utils.keccak256(valA)
const valBwin = ethers.utils.keccak256(0x600D60A7)
```

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       9. 输入 tx1 = await casino.proposeBet(hashA,{ value: 1e5}) ，回车

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       10. 输入 tx2 = await casino.acceptBet(hashA, valBwin, {value: 1e5})，回车

<figure><img src="../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       11. 最后去 Metamask 上通过区块浏览器查看

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_红框处即合约地址，一共3次调用_
{% endhint %}

#### &#x20;       12. 删除 <mark style="color:red;">.env</mark>(此步非常重要！！！)



### 四、上传到Github

#### &#x20;           参照 [<mark style="color:red;">任务中所用平台和工具</mark>](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) <mark style="color:red;"></mark> 中 replit 如何提交到Github的操作流程

{% hint style="warning" %}
_切记删除与私钥相关的东西_
{% endhint %}



### 五、提交

#### &#x20;          本周能证明任务完成的地址：

&#x20;               1）合约地址： [https://blockscout.com/optimism/goerli/address/ 你的合约地址](https://blockscout.com/optimism/goerli/address/0x7259eBbf74e9C3baBc94233BB5A73dbe67Ba8747)

&#x20;               2）github地址

{% hint style="info" %}
_<mark style="color:red;">合约地址</mark> 在第三部分查看，点_ [_<mark style="color:red;">此处</mark>_](../preparation/ren-wu-ti-jiao-ren-wu-wan-cheng-shi-kan.md) _到任务提交部分_

_（若找不到合约地址，请参看_ [_<mark style="color:red;">任务中所用平台和工具</mark>_](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) _中钱包相关部分的如何寻找合约地址）_
{% endhint %}

