---
description: 《How to Develop an NFT Smart Contract (ERC721) with Alchemy》
cover: ../.gitbook/assets/task-cover.jpg
coverY: 0
---

# 第一周（Goerli测试网）

{% hint style="warning" %}
<mark style="color:red;">**特别声明：**</mark> 每周任务前，请参看 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) ，无论此周任务需要用到多少！！！
{% endhint %}

### 一、准备工作

#### &#x20;       1. 通过 [_任务中所用平台和工具_](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 的步骤，在 [Alchemy](https://dashboard.alchemy.com/) 上创建一个 ETH Goerli测试网 的APP，并保留此页面

#### &#x20;       2. 将Alchemy Goerli网络添加到Metamask钱包

&#x20;           1）点开钱包，选择网络，点击 添加网络

&#x20;                                                     ![](<../.gitbook/assets/image (34) (1).png>)

&#x20;           2）填写 Goerli 网络和 前面在Alchemy上生成的RPC URL 信息，并保存

&#x20;                                                    ![](<../.gitbook/assets/image (33) (1) (1).png>)

{% hint style="info" %}
_记得将钱包切换到_ <mark style="color:red;"></mark> <mark style="color:red;"></mark>_<mark style="color:red;">Alchemy</mark>_ <mark style="color:red;"></mark><mark style="color:red;"></mark> <mark style="color:red;"></mark>_<mark style="color:red;">Goerli测试网</mark>_
{% endhint %}

#### &#x20;       3. 在 [https://goerlifaucet.com/](https://goerlifaucet.com/) 上，登录Alchemy账号，输入钱包地址领取测试ETH

<figure><img src="../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 在IPFS上上传NFT和元数据

&#x20;           1）进入 [https://filebase.com/](https://filebase.com/) ，注册。登录后，单击左侧菜单 _<mark style="color:red;">Buckets</mark>_ 按钮，然后选 _<mark style="color:red;">Create Bucket</mark>_ ，创建一个新存储桶(自己取名字，重名不行)&#x20;

<figure><img src="../.gitbook/assets/image (29) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;                                                         ![](<../.gitbook/assets/image (35) (1).png>)

&#x20;           2）点击进入刚创建的Bucket，单击右上的 _<mark style="color:red;">Upload</mark>_ 按钮 ，然后上传您要用于 NFT 的图像

<figure><img src="../.gitbook/assets/image (7) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_a.可上传本地图片，或者网上下载的免费图片（ 比如_ [_https://www.pexels.com/zh-cn/_](https://www.pexels.com/zh-cn/) _）_

_b.上传时会显示进度条，等待完成后刷新页面_
{% endhint %}

&#x20;           3）单击名字，进入详细页面，复制 _<mark style="color:red;">IPFS 网关 URL</mark>_，暂时保留页面

<figure><img src="../.gitbook/assets/image (31) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           4）转到电脑桌面，右键新建创建文本文档，重命名为“metadata.json”（连带后缀全改）,复制以下代码进去，保存

{% hint style="info" %}
_a. 如果看不到文件后缀，比如系统是win10，请自行百度或者谷歌搜索 <mark style="color:red;">win10如何显示文件后缀名</mark> ，进行设置更改_

_b. 将上一步复制的URL，粘贴替换下面的 ”image“后面_
{% endhint %}

```json
{ 
  "description": "This NFT proves I've created and deployed my first ERC721 smart contract on Goerli Testnet with Alchemy",
  "external_url": "Alchemy.com",
  "image": "https://ipfs.filebase.io/ipfs/QmPXigwDiUevneEJtgSDWt1GEm82TT42gw2D4WXChZyRZf",
  "name": "A Beautiful NFT", 
  "attributes": [
    {
      "trait_type": "Base", 
      "value": "Starfish"
    }, 
    {
      "trait_type": "Eyes", 
      "value": "Big"
    }, 
    {
      "trait_type": "Mouth", 
      "value": "Surprised"
    }, 
    {
      "trait_type": "Level", 
      "value": 5
    }, 
    {
      "trait_type": "Stamina", 
      "value": 1.4
    }, 
    {
      "trait_type": "Personality", 
      "value": "Sad"
    }, 
    {
      "display_type": "boost_number", 
      "trait_type": "Aqua Power", 
      "value": 40
    }, 
    {
      "display_type": "boost_percentage", 
      "trait_type": "Stamina Increase", 
      "value": 10
    }, 
    {
      "display_type": "number", 
      "trait_type": "Generation", 
      "value": 2
    }
  ]
}
```

&#x20;           5）回到 Filebase ，退到上一页，回到图片的桶页面，然后再次点击 _<mark style="color:red;">Upload</mark>_ 进行上传，这次选择的是桌面的metadata.json

<figure><img src="../.gitbook/assets/image (26) (2).png" alt=""><figcaption></figcaption></figure>

&#x20;           6）上传成功后，复制一下metadata.json的 _<mark style="color:red;">CID</mark>_，后面需要用到

<figure><img src="../.gitbook/assets/image (45) (1).png" alt=""><figcaption></figcaption></figure>



### 二、项目代码初始化

#### &#x20;       1. 进入 [Open Zeppelin](https://docs.openzeppelin.com/contracts/4.x/wizard) 智能合约向导页面，将看到以下编辑器

<figure><img src="../.gitbook/assets/image (41) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 点击左上角 ERC721 按钮，输入 Name 和 Symbol

<figure><img src="../.gitbook/assets/image (1) (1) (3).png" alt=""><figcaption></figcaption></figure>

#### &#x20;      3. 勾选如下的NFT功能

<figure><img src="../.gitbook/assets/image (42) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 在 OpenZeppelin Wizard 编辑器的顶部，点击“Open in Remix”按钮，在浏览器的新选项卡中打开 REMIX IDE

<figure><img src="../.gitbook/assets/image (39) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 打开后，等一小会会自动跳到智能合约代码界面

<figure><img src="../.gitbook/assets/image (15) (2).png" alt=""><figcaption></figcaption></figure>

&#x20;           1）在第14行填入 <mark style="color:red;">uint256 MAX\_SUPPLY = 100000;</mark>

<figure><img src="../.gitbook/assets/image (40) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           <mark style="color:red;"></mark>            2）删除17行的 <mark style="color:red;">onlyOwner</mark> ，否则只允许智能合约的所有者（部署智能合约的钱包地址）铸造 NFT

<figure><img src="../.gitbook/assets/image (46) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           3）在第19行添加 <mark style="color:red;">require(\_tokenIdCounter.current() <= MAX\_SUPPLY, "I'm sorry we reached the cap");</mark>

<figure><img src="../.gitbook/assets/image (14) (2).png" alt=""><figcaption></figcaption></figure>



### 三、部署智能合约

#### &#x20;       1. 回到 Remix，点击页面左侧的Solidity compiler，版本选0.8.4，勾选Auto compile，然后点击“Compile”按钮

<figure><img src="../.gitbook/assets/image (11) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 左边栏点击“Deploy & Run Transactions”菜单

{% hint style="info" %}
_Environment下拉菜单中选择 <mark style="color:red;">Injected provider- Metamask</mark>_

_contract选择 <mark style="color:red;">Alchemy-contracts</mark> 这个_

_点击 <mark style="color:red;">Deploy</mark>_
{% endhint %}

<figure><img src="../.gitbook/assets/image (8) (3).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 将出现 Metamask 弹出窗口，需支付一些 Gas 费用，单击“确认”

<figure><img src="../.gitbook/assets/image (43) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 如果一切都按预期工作，大约20 秒后，您应该会在左下的 _<mark style="color:red;">Deployed Contracts</mark>_ 处看到该合约

<figure><img src="../.gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_若部署合约出现下面类似的错，请检查钱包的网络是否选对_

&#x20;   {

&#x20;        "code": -32000,&#x20;

&#x20;        "message": "execution reverted"&#x20;

&#x20;   }
{% endhint %}



### 四、测试智能合约功能

#### &#x20;       1. 点开合约

&#x20;                                           ![](<../.gitbook/assets/image (36) (1).png>)

#### &#x20;       2. 点击 _<mark style="color:red;">safeMint</mark>_ 下拉框

{% hint style="info" %}
_to 填入 <mark style="color:red;">你的钱包地址</mark>_

_uri 填入_ _<mark style="color:red;">ipfs://准备工作部分metadata.json的CID</mark>_
{% endhint %}

&#x20;                                            ![](<../.gitbook/assets/image (20) (1) (2).png>)

&#x20;           1）点击transact，会花一点手续费，等一会成功了会在右侧显示绿勾

<figure><img src="../.gitbook/assets/image (37) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 将钱包地址复制并粘贴到 _<mark style="color:red;">balanceOf</mark>_ 方法输入中点击call，它应该显示您有 1 个 NFT

&#x20;                                             ![](<../.gitbook/assets/image (30) (1) (1).png>)

#### &#x20;       4. 在tokenUri 插入“0”作为 id 参数，点击call，它应该显示你的 tokenURI

&#x20;                                             ![](<../.gitbook/assets/image (44).png>)



### 五、展示NFT

{% hint style="warning" %}
_由于_ [_https://testnets.opensea.io/_](https://testnets.opensea.io/) _目前不支持显示Goerli上的，选择采用_ [_https://goerli.pixxiti.com/_](https://goerli.pixxiti.com/)
{% endhint %}

#### &#x20;       1. 进入 [https://goerli.pixxiti.com/](https://goerli.pixxiti.com/) ，登录自己钱包，然后单击 _<mark style="color:red;">View your NFTs</mark>_ ，您应该会在那里看到您新铸造的 NFT

<figure><img src="../.gitbook/assets/image (23) (2) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           1）红圈图片为刚mint的图片

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 如果图像尚不可见，点击进去，然后单击右下的“Refresh”按钮，大约需要几分钟

<figure><img src="../.gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>



### 六、额外挑战（可选做）

{% hint style="info" %}
_挑战课题： 如何限制每个钱包的 <mark style="color:red;">mint</mark> 数量_
{% endhint %}

#### &#x20;       1. 在REMIX的代码中加入以下内容

&#x20;           1）在  <mark style="color:red;">uint256 MAX\_SUPPLY = 10000;</mark> 下面添加：

```solidity
uint256 walletLimit = 2; //limit for per wallet
mapping(address => uint) public walletMints;
```

&#x20;          2）在 <mark style="color:red;">require(tokenId <= MAX\_SUPPLY, "I'm sorry we reached the cap");</mark> 下面添加：

```solidity
require(walletMints[msg.sender] < walletLimit, "Max Mint per wallet reached");
walletMints[msg.sender]++;
```

&#x20;            如图：

<figure><img src="../.gitbook/assets/image (6) (1) (2).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_上面 <mark style="color:red;">walletLimt</mark> 的值可以自行修改，后面测试符合此值即可_
{% endhint %}

#### &#x20;       2. 按 [部署智能合约](di-yi-zhou-goerli-ce-shi-wang.md#san-bu-shu-zhi-neng-he-yue) 流程以及 [测试智能合约功能](di-yi-zhou-goerli-ce-shi-wang.md#si-ce-shi-zhi-neng-he-yue-gong-neng) 的 [safeMint](di-yi-zhou-goerli-ce-shi-wang.md#2.-dian-ji-safemint-xia-la-kuang) 流程操作

&#x20;           1）此处测试需结合上一步设置的 walletLimit 数量。比如设置为2，在to地址和url不改变的情况下，当连续mint成功两次后，查看Metamask上的活动

&#x20;                                                 ![](<../.gitbook/assets/image (17) (2) (1).png>)

&#x20;            2）第三次点transact就会出现以下错误（红框处和代码中的提醒一致）

<figure><img src="../.gitbook/assets/image (38) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
如何证明完成了额外挑战：&#x20;

_a. 给出合约地址，即可通过区块浏览器查看到进行了多少次safe mint_

_b. 将设置了walletLimit数量的代码、合约中几次safe mint、以及最后一次出错的几个截图发送到twitter上，并将此twitter链接填在提交表格中的 ”Share the URL of your reflection!“处_
{% endhint %}



### 七、提交

#### &#x20;          此周能证明完成任务的地址

&#x20;           1）合约地址： [https://goerli.etherscan.io/address/](https://goerli.etherscan.io/address/)您的合约地址

{% hint style="info" %}
_<mark style="color:red;">合约地址</mark> 在第三部分查看，点_ [_<mark style="color:red;">此处</mark>_](../preparation/ren-wu-ti-jiao-ren-wu-wan-cheng-shi-kan.md) _到任务提交部分_

_（若找不到合约地址，请参看_ [_<mark style="color:red;">任务中所用平台和工具</mark>_](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) _中钱包相关部分的如何寻找合约地址；_

_若完成了第六部分，则用六中产生的合约地址，当然也别忘记完成其他的功能测试才提交）_
{% endhint %}

