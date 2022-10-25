---
description: 《Connect APIs to your Smart Contracts using Chainlink》
cover: ../.gitbook/assets/task-cover.jpg
coverY: 0
---

# 第五周（Goerli测试网）

{% hint style="warning" %}
<mark style="color:red;">**特别声明：**</mark> 每周任务前，请参看 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) ，无论此周任务需要用到多少！！！
{% endhint %}

### 一、准备工作

#### &#x20;       1. 先将Metamask钱包网络切换到Goerli测试网

&#x20;                                                    ![](<../.gitbook/assets/image (72).png>)

#### &#x20;       2. 进入 [https://faucets.chain.link/](https://faucets.chain.link/) ，连接你的钱包，验证后，点击Send Request获取测试以太坊和link 代币

<figure><img src="../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 领水后，进入 [https://vrf.chain.link/](https://vrf.chain.link/)&#x20;

&#x20;           1）连接你的钱包，选择 Create Subscription

<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

&#x20;           2）点击后，继续点 Create Subscription

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

&#x20;            3）然后会消耗少量测试eth的gas费，用于创建

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

&#x20;           4）成功后，点击 Add Funds

<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

&#x20;           5）填入用于后面的Link数量，4以上应该都可以，再点 Add funds 确认

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

&#x20;           6）同样需要一些测试eth手续费

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

&#x20;           7）成功后，点击 Add consumers

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

&#x20;           8）变成

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_注意： 上图页面要保留，红圈处ID后面会用到_
{% endhint %}



### 二、项目代码初始化

#### &#x20;       1. 进入 [https://remix.ethereum.org/](https://remix.ethereum.org/)&#x20;

&#x20;            1）选择新建工作区，name随意，我设置为myweek5

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

&#x20;            2）把Contracts和Test文件夹里的文件全部删除

&#x20;                                                   ![](<../.gitbook/assets/image (24).png>)

&#x20;           3）删掉后，在contracts目录点右键，选 新建文件

{% hint style="info" %}
_名字随意（最好别和工作区名字相同），我命名为week5test.sol_
{% endhint %}

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

&#x20;           4）贴入下面的代码

```solidity
// SPDX-License-Identifier: GPL-3.0

pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

// Chainlink Imports
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
// This import includes functions from both ./KeeperBase.sol and
// ./interfaces/KeeperCompatibleInterface.sol
import "@chainlink/contracts/src/v0.8/KeeperCompatible.sol";

import "@chainlink/contracts/src/v0.8/interfaces/VRFCoordinatorV2Interface.sol";
import "@chainlink/contracts/src/v0.8/VRFConsumerBaseV2.sol";

// Dev imports. This only works on a local dev network
// and will not work on any test or main livenets.
import "hardhat/console.sol";

contract ABC is ERC721URIStorage, Ownable, VRFConsumerBaseV2, KeeperCompatibleInterface   {
    using Counters for Counters.Counter;

    Counters.Counter private _tokenIdCounter;
    uint public interval;
    uint public lastTimeStamp;

    AggregatorV3Interface public priceFeed;
    int256 public currentPrice;

    // IPFS URIs for the dynamic nft graphics/metadata.
    // NOTE: These connect to my IPFS Companion node.
    // You should upload the contents of the /ipfs folder to your own node for development.
    string[] bullUrisIpfs = [
        "https://ipfs.io/ipfs/QmRXyfi3oNZCubDxiVFre3kLZ8XeGt6pQsnAQRZ7akhSNs?filename=gamer_bull.json",
        "https://ipfs.io/ipfs/QmRJVFeMrtYS2CUVUM2cHJpBV5aX2xurpnsfZxLTTQbiD3?filename=party_bull.json",
        "https://ipfs.io/ipfs/QmdcURmN1kEEtKgnbkVJJ8hrmsSWHpZvLkRgsKKoiWvW9g?filename=simple_bull.json"
    ];
    string[] bearUrisIpfs = [
        "https://ipfs.io/ipfs/Qmdx9Hx7FCDZGExyjLR6vYcnutUR8KhBZBnZfAPHiUommN?filename=beanie_bear.json",
        "https://ipfs.io/ipfs/QmTVLyTSuiKGUEmb88BgXG3qNC8YgpHZiFbjHrXKH3QHEu?filename=coolio_bear.json",
        "https://ipfs.io/ipfs/QmbKhBXVWmwrYsTPFYfroR2N7NAekAMxHUVg2CWks7i9qj?filename=simple_bear.json"
    ];


    // random
    VRFCoordinatorV2Interface COORDINATOR;

    // Your subscription ID.
    uint64 s_subscriptionId;

    // Goerli coordinator. For other networks,
    // see https://docs.chain.link/docs/vrf-contracts/#configurations
    address vrfCoordinator = 0x2Ca8E0C643bDe4C2E08ab1fA0da3401AdAD7734D;

    // The gas lane to use, which specifies the maximum gas price to bump to.
    // For a list of available gas lanes on each network,
    // see https://docs.chain.link/docs/vrf-contracts/#configurations
    bytes32 keyHash = 0x79d3d8832d904592c0bf9818b621522c988bb8b0c05cdc3b15aea1b6e8db0c15;

    // Depends on the number of requested values that you want sent to the
    // fulfillRandomWords() function. Storing each word costs about 20,000 gas,
    // so 100,000 is a safe default for this example contract. Test and adjust
    // this limit based on the network that you select, the size of the request,
    // and the processing of the callback request in the fulfillRandomWords()
    // function.
    uint32 callbackGasLimit = 100000;

    // The default is 3, but you can set this higher.
    uint16 requestConfirmations = 3;

    // For this example, retrieve 2 random values in one request.
    // Cannot exceed VRFCoordinatorV2.MAX_NUM_WORDS.
    uint32 numWords =  2;
    uint256[] public s_randomWords;
    uint256 public s_requestId;

    event TokensUpdated(string marketTrend);

    constructor(uint updateInterval, address _priceFeed, uint64 subscriptionId) ERC721("Bull&Bear", "BBTK") VRFConsumerBaseV2(vrfCoordinator) {
        interval = updateInterval;
        lastTimeStamp = block.timestamp;

        priceFeed = AggregatorV3Interface(_priceFeed);
        currentPrice = getLatestPrice();

        COORDINATOR = VRFCoordinatorV2Interface(vrfCoordinator);
        s_subscriptionId = subscriptionId;
    }

    function safeMint(address to) public {
        // Current counter value will be the minted token's token ID.
        uint256 tokenId = _tokenIdCounter.current();

        // Increment it so next time it's correct when we call .current()
        _tokenIdCounter.increment();

        // Mint the token
        _safeMint(to, tokenId);

        // Default to a bull NFT
        string memory defaultUri = bullUrisIpfs[s_randomWords[0]%3];
        _setTokenURI(tokenId, defaultUri);

        console.log(
            "DONE!!! minted token ",
            tokenId,
            " and assigned token url: ",
            defaultUri
        );
    }

    function checkUpkeep(bytes calldata) external view override returns (bool upkeepNeeded, bytes memory /*performData*/){
        upkeepNeeded = (block.timestamp - lastTimeStamp) > interval;
    }

    function performUpkeep(bytes calldata) external override{
        if((block.timestamp - lastTimeStamp) > interval){
            lastTimeStamp = block.timestamp;
            int latestPrice = getLatestPrice();

            if(latestPrice == currentPrice){
                return;
            }else if(latestPrice < currentPrice){
                updateAllTokenUris("bears");
            }else{
                updateAllTokenUris("bull");
            }

            currentPrice = latestPrice;
        }
    }

    function getLatestPrice() public view returns(int256){
        (,
        int price,
        ,
        ,) = priceFeed.latestRoundData();
        return price;
    }

    function updateAllTokenUris(string memory trend) internal{
        if(compareStrings("bears", trend)){
            for(uint i=0; i< _tokenIdCounter.current(); i++){
                _setTokenURI(i,bearUrisIpfs[s_randomWords[0]%3]);
            }
        }else {
            for(uint i=0; i< _tokenIdCounter.current(); i++){
                _setTokenURI(i,bullUrisIpfs[s_randomWords[0]%3]);
            }
        }

        emit TokensUpdated(trend);
    }

    function setInterval(uint256 newInterval) public onlyOwner{
        interval = newInterval;
    }

    function setPriceFeed(address newFeed) public onlyOwner{
        priceFeed = AggregatorV3Interface(newFeed);
    }

    function compareStrings(string memory a, string memory b) internal pure returns (bool){
        return keccak256(abi.encodePacked(a)) == keccak256(abi.encodePacked(b));
    }

    // The following functions are overrides required by Solidity.
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override(ERC721) {
        super._beforeTokenTransfer(from, to, tokenId);
    }

    function _burn(uint256 tokenId)
        internal
        override(ERC721URIStorage)
    {
        super._burn(tokenId);
    }

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }

        // Assumes the subscription is funded sufficiently.
    function requestRandomWords() external onlyOwner {
        // Will revert if subscription is not set and funded.
        s_requestId = COORDINATOR.requestRandomWords(
        keyHash,
        s_subscriptionId,
        requestConfirmations,
        callbackGasLimit,
        numWords
        );
    }

    function fulfillRandomWords(
        uint256, /* requestId */
        uint256[] memory randomWords
    ) internal override {
        s_randomWords = randomWords;
    }
}
```

#### &#x20;       2. 选择左边栏的Solidity Compiler，然后选择 0.8.4+的编译器，再点 Compile

&#x20;                                            ![](<../.gitbook/assets/image (85).png>)

{% hint style="info" %}
_会有几个warning，可以忽略不管_
{% endhint %}

&#x20;      &#x20;

### 三、部署智能合约

#### &#x20;       1. 选择左边栏的 <mark style="color:red;">Deploy & Run transactions</mark>

&#x20;           1）点开 Deploy

&#x20;                                               ![](<../.gitbook/assets/image (84).png>)

{% hint style="info" %}
_a. ENVIROMENT选择 <mark style="color:red;">Injected Provider-Metamask</mark>_

_b. Contract选择 <mark style="color:red;">ABC-contracts/...</mark> 这个_

_c. UPDATEINTERVAL处填入<mark style="color:red;">10</mark>_

_d._ _\_PRICEFEED处填入<mark style="color:red;">0x779877A7B0D9E8603169DdbD7836e478b4624789</mark>_

_e. SUBSCRIPTIONID处填入前面创建好的chainlink subscription的id（我的是<mark style="color:red;">2281</mark>）_
{% endhint %}

&#x20;            2）点击 transact，等待合约部署成功（会消耗一点测试eth）

{% hint style="warning" %}
_若出现下图错误，则很可能当前钱包网络不是eth goerli测试网，请切换_

&#x20;                                __                                 ![](<../.gitbook/assets/image (18).png>)__
{% endhint %}

&#x20;           3）成功后，会在左下角显示合约，点击复制

&#x20;                                          ![](<../.gitbook/assets/image (12) (1).png>)

#### &#x20;       2. 回到 [准备工作](di-wu-zhou-goerli-ce-shi-wang.md#yi-zhun-bei-gong-zuo) 留着的 Chainlink 页面

&#x20;           1）将复制的合约地址添加进Consumer

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

&#x20;           2）同样会消耗一点gas

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           3）成功添加后，显示如下

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>



### 四、测试智能合约功能

#### &#x20;       1. 点开合约地址

&#x20;                                             ![](<../.gitbook/assets/image (11) (1).png>)

#### &#x20;       2. 测试获取随机数

&#x20;           1）点击 _<mark style="color:red;">requestRandomWords</mark>_ 方法来获取随机数，然后等Metamask确定交易完成

&#x20;                                              ![](<../.gitbook/assets/image (13) (1).png>)

&#x20;            2）确认后，大约需要等1分钟，右边若显示绿勾则表示获取成功

<figure><img src="../.gitbook/assets/image (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           3）选到 _<mark style="color:red;">s\_randomWords</mark>_ ，点开输入0，点击Call。若成功，则会显示出 _<mark style="color:red;">随机数</mark>_

&#x20;                                             ![](<../.gitbook/assets/image (15) (1).png>)

{% hint style="info" %}
_a.随机数需要等好一会（30秒-几分钟不等），点call才会出现随机串，否则会一直显示类似_

&#x20;  __   {

&#x20;        "code": -32000,&#x20;

&#x20;        "message": "execution reverted"&#x20;

&#x20;   }

_b.可重复上面的操作，点击 <mark style="color:red;">requestRandomWords</mark> 方法重新获取随机数，需要多等几分钟才能获取到新的随机数_
{% endhint %}

&#x20;           4）很多朋友反映不知道怎么判断啥时候可以拿到随机值，这里增加一个方法。在前面的chainlink的vrf页面上，可以看到已经绑定的consumer，其中的一项“Total spent LINK”，只要出现了消耗值，即表示随机数生成成功，如图

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 测试mint

&#x20;           1）点开 _<mark style="color:red;">safeMint</mark>_ ，填入钱包地址，点击 transact

&#x20;                                              ![](<../.gitbook/assets/image (1) (1) (1).png>)

&#x20;           2）同样要消耗一点gas，直到右边出现绿勾

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           3）mint成功以后，我们在 _<mark style="color:red;">tokenURI</mark>_ 这边输入0，点击 call，来获取我们mint的NFT的元数据信息，应是前面代码中3个之一

&#x20;                                                ![](<../.gitbook/assets/image (4) (1) (1).png>)

<figure><img src="../.gitbook/assets/image (3) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 测试设置预言机

&#x20;           1）在 _<mark style="color:red;">setPriceFeed</mark>_ 方法里输入Link预言机的地址，点击 transact

{% hint style="info" %}
_这里填的是Link与ETH预言的合约地址，经查阅为_

<mark style="color:red;">0xb4c4a493AB6356497713A78FFA6c60FB53517c63</mark>

_（可在_ [_https://docs.chain.link/docs/data-feeds/price-feeds/addresses/?network=ethereum_](https://docs.chain.link/docs/data-feeds/price-feeds/addresses/?network=ethereum) _上去查询）_
{% endhint %}

&#x20;                                          ![](<../.gitbook/assets/image (6) (1) (3).png>)

&#x20;           2）同样消耗一点gas，成功会出现绿勾

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;           3）点击一下 _<mark style="color:red;">getLatestPrice</mark>_ 方法，可以看到int256后面就是最新的以太坊价格，其中前4位是个位数，后面8位为小数，这是因为以太坊不支持小数，所以价格就不包含小数点

&#x20;                                            ![](<../.gitbook/assets/image (14).png>)

&#x20;           4）在 _<mark style="color:red;">performUpkeep</mark>_ 方法的参数里输入“\[]”，点击 call，手动来触发价格更新方法

&#x20;                                             ![](<../.gitbook/assets/image (17) (1) (2).png>)

&#x20;           5\)  上一步方法里也会更新我们的NFT元数据，再次点击 _<mark style="color:red;">tokenURI</mark>_ 处的 call，获取Token Id 为0的NFT的元数据

&#x20;                                             ![](<../.gitbook/assets/image (7) (1) (1).png>)

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_因为我们预言机从BTC的价格变到了ETH价格，我们代码里设置如果价格降低，元数据就是三个bear中的一个，反之如果预言机价格变高了，则是bull中的一个。_

_另外：附上LINK预言机USD的合约地址_<mark style="color:red;">`0x48731cF7e84dc94C5f84577882c14Be11a5B7456`</mark>

_各位可以用这三个预言机合约多试几次，看看NFT的动态改变_
{% endhint %}



### 五、展示NFT

#### &#x20;       有兴趣的可以自己去 [https://goerli.pixxiti.com/](https://goerli.pixxiti.com/) 看自己mint的nft，登录钱包，刷新元数据（参看第一周的 _<mark style="color:red;"></mark>_ [_<mark style="color:red;">展示NFT</mark>_](di-yi-zhou-goerli-ce-shi-wang.md#wu-zhan-shi-nft) _<mark style="color:red;"></mark>_ 部分）



### 六、提交

#### &#x20;       本周证明任务完成的地址

&#x20;           1）合约地址：[https://goerli.etherscan.io/address/](https://goerli.etherscan.io/address/)你的合约地址

{% hint style="info" %}
_<mark style="color:red;">合约地址</mark>可参看第三部分，点_ [_<mark style="color:red;">此</mark>_](../preparation/ren-wu-ti-jiao-ren-wu-wan-cheng-shi-kan.md)_<mark style="color:red;">处</mark> 跳到任务提交部分_

_（若找不到合约地址，请参看_ [_<mark style="color:red;">任务中所用平台和工具</mark>_](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) _中钱包相关部分的如何寻找合约地址）_
{% endhint %}

