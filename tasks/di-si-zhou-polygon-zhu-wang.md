# 第四周（Polygon主网）

{% hint style="warning" %}
<mark style="color:red;">**特别声明：**</mark> 每周任务前，请参看 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) ，无论此周任务需要用到多少！！！
{% endhint %}

### 一、准备工作

#### &#x20;       1.  完成 [任务准备](../preparation/ren-wu-zhun-bei-zuo-qian-bi-kan.md) 中的第4、6步骤，项目命名为 road-to-web3-week4（可以自定义）

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 在 [任务中所用平台和工具](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) 的流程中，拿到Alchemy上Polygon主网App的 <mark style="color:red;">HTTPS、API KEY</mark>



### 二、项目代码初始化

{% hint style="danger" %}
**！！！特别提醒： 有时会出现网络不畅，导致shell退到第一层目录，切记要切换到对应的目录进行操作，注意观察（现象是replit上面的RUN会变成Working）**
{% endhint %}

#### &#x20;       1. 点击右侧Shell，输入 npx create-next-app -e with-tailwindcss hymngallery ，回车（中间选择时直接回车）

{% hint style="info" %}
_命令最后的hymngallery可以任意修改_
{% endhint %}

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2.  输入 cd hymngalley ，回车

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 输入 npm i react-infinite-scroll-component ，回车

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4.左边栏找到hymngallery/pages文件夹下的\_app.tsx和index.tsx文件，都修改后缀为.jsx

&#x20;                                                      ![](<../.gitbook/assets/image (13).png>)

&#x20;                                                      ![](<../.gitbook/assets/image (17).png>)

&#x20;              同理，index.jsx

&#x20;                                                     ![](<../.gitbook/assets/image (20).png>)

#### &#x20;       5. 点击\_app.jsx，自动到中间内容，替换成以下内容（记得保存）

```jsx
import '../styles/globals.css'

function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}

export default MyApp
```

#### &#x20;       6. 点击index.jsx，自动到中间内容，替换成以下内容（记得保存）

```jsx
import { NFTCard } from "./nftCard"
import { useState } from 'react'
import InfiniteScroll from "react-infinite-scroll-component";

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([]);
  const [fetchForCollection, setFetchForCollection]=useState(false);
  const [res, setRes]=useState(0);

  const fetchNFTs = async() => {
    let nfts; 
    setRes(1);
    console.log("fetching nfts");
    const api_key = "1234"
    const baseURL = `https://polygon-mainnet.g.alchemyapi.io/v2/${api_key}/getNFTs/`;
    var requestOptions = {
        method: 'GET'
      };
     
    if (!collection.length) {
    
      const fetchURL = `${baseURL}?owner=${wallet}`;
  
      nfts = await fetch(fetchURL, requestOptions).then(
        data => {setRes(2); return data.json();}
      )
    } else {
      console.log("fetching nfts for collection owned by address")
      const fetchURL = `${baseURL}?owner=${wallet}&contractAddresses%5B%5D=${collection}`;
      nfts= await fetch(fetchURL, requestOptions).then(
        data => {setRes(2); return data.json();}
      )
    }
  
    if (nfts) {
      console.log("nfts:", nfts)
      setNFTs(nfts.ownedNfts)
    }
  }
  
  const fetchNFTsForCollection = async () => {
    if (collection.length) {
      setRes(1);
      var requestOptions = {
        method: 'GET'
      };
      const api_key = "1234"
      const baseURL = `https://polygon-mainnet.g.alchemyapi.io/v2/${api_key}/getNFTsForCollection/`;
      const fetchURL = `${baseURL}?contractAddress=${collection}&withMetadata=${"true"}`;
      const nfts = await fetch(fetchURL, requestOptions).then(data => {setRes(2); return data.json();})
      if (nfts) {
        console.log("NFTs in collection:", nfts)
        setNFTs(nfts.nfts)
      }
    }
  }

  return (
    <div className="flex flex-col py-8 gap-y-3 bg-[url('/back.jpg')] bg-cover bg-no-repeat w-full h-screen">
      <div className="flex justify-between">
        <div className="flex flex-col justify-center items-center">
        {
          (wallet || collection) ? ( 
            NFTs.length ? (
              <div className="absolute left-[140px] top-[80px]">
                <h1 className="text-white font-bold transform -skew-y-20 -skew-x-12 text-6xl">My Collections</h1>
                <h2 className="text-white font-bold transform -skew-y-20 -skew-x-12 text-4xl">Have {NFTs.length} NFTs</h2>
              </div>
              ) : ( res == 1 ? (
                    <div className="absolute left-[240px] top-[100px]">
                      <h2 className="text-white font-bold transform -skew-y-20 -skew-x-12 text-4xl"> Waiting... </h2>
                    </div>
                  ) : ( res == 2 ? (
                      <div className="absolute left-[180px] top-[100px]">
                        <h2 className="text-white font-bold transform -skew-y-20 -skew-x-12 text-4xl">No NFT to show</h2>
                      </div>
                      ) : (
                        <div className="hidden"></div>
                      )
                  )
                )
          ) : (
            <div className="hidden"></div>
          )
        }
        </div>
        <div className="flex flex-col justify-center items-center gap-y-4 top-[60px] right-[60px] absolute">
          <input className="border-4 border-indigo-500/75 w-80 rounded-xl" disabled={fetchForCollection} type={"text"} onChange={(e)=>{setWalletAddress(e.target.value);setCollectionAddress("");}} value={wallet} placeholder="Input your wallet address"></input>
          <input className="border-4 border-indigo-500/75 w-80 rounded-xl" disabled={!fetchForCollection} type={"text"} onChange={(e)=>{setCollectionAddress(e.target.value);setWalletAddress("");}} value={collection} placeholder="Add the collection address"></input>
          <label className="text-green-600 font-semibold"><input onChange={(e)=>{setFetchForCollection(e.target.checked)}} type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
          <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-full w-40 font-bold shadow-inner bottom-2 border-solid"} 
            onClick={ () => {
              if (fetchForCollection) {
                fetchNFTsForCollection()
              } else {
                fetchNFTs()
              }
            }
          }> Show NFTs </button>
        </div>
      </div>
      
      <div className="flex flex-col justify-start gap-y-12 mt-4 gap-x-2 absolute bottom-[20px] left-[50px]">
      {
        
        NFTs.length ? ( 
          <div className="flex m-4">
            <InfiniteScroll
              dataLength={100}
              height={500}
            > 
              { 
                NFTs.map(nft => {
                  return (
                    <NFTCard nft={nft}></NFTCard>          
                  )
                })
              }
            </InfiniteScroll>
          </div> 
        ) : ( 
          <div className="hidden"></div>
        )
      }
      </div>
    </div>
  )
}

export default Home
```

{% hint style="info" %}
_a. 粘贴过去后，有2处API KEY需要修改，分别是index.jsx的第15行和第47行（默认赋值为1234的），改为_ [_准备工作_](di-si-zhou-polygon-zhu-wang.md#yi-zhun-bei-gong-zuo) _中拿到的 <mark style="color:red;">API KEY</mark>_

&#x20;               ![](<../.gitbook/assets/image (75).png>)

&#x20;               ![](<../.gitbook/assets/image (8).png>)

_b. （可选）代码默认是在 Polygon 主网，若要换到其他网络查看拥有的NFT，请将index.jsx的第16行和第48行的链接前缀替换为对应的 Alchemy APP上拿到的HTTPS前缀_

_（前缀是指，从 https:// 后面一直到 alchemyapi.io 前面的一串字符）_

_比如想切换到 Polygon Mumbai 测试网，以16行为例，应将：_

&#x20;               ![](<../.gitbook/assets/image (4).png>)

&#x20;    _改为：_

&#x20;               ![](<../.gitbook/assets/image (15).png>)

&#x20;    _每个网络对应的 <mark style="color:red;">API KEY</mark> 也是不同的，所以15行的 API KEY 也要去Alchemy APP上查看Polygon mumbai 的API KEY并替换_
{% endhint %}

#### &#x20;       7. 在hymngallery/pages文件夹下新增nftCard.jsx文件

&#x20;                                               ![](<../.gitbook/assets/image (1) (1).png>)

#### &#x20;       8. 回车，自动到内容，填入以下内容（记得保存）

```jsx
import { useEffect,useState } from 'react'

export const NFTCard = ({ nft }) => {
    const [isVideo, setIsVideo] = useState(false);
    const filename = nft.media[0].gateway;
    const filetype = filename.split('.').pop().toLowerCase();

    useEffect(() => {
        if (filetype == "mp4" || filetype == "avi" || filetype == "rm" || filetype == "mov") {
            setIsVideo(true);
        }
    },[]);
    
    return (
        <div className="flex justify-center gap-x-2 p-4">
            <div className="w-80">
                {
                    isVideo ? (
                        <embed className="object-cover w-80 h-80 shadow-lg border-4 border-solid rounded-3xl" src={nft.media[0].gateway}></embed>
                    ) : (
                        <img className="object-cover w-80 h-80 shadow-lg border-4 border-solid rounded-3xl" src={nft.media[0].gateway} ></img>
                    )
                }
            </div>
            <div className="flex flex-col gap-10 px-2 py-3 bg-sky-600 shadow-lg border-4 border-solid rounded-3xl">
                    <h2 className="text-xl text-yellow-600 w-650px text-center font-bold">{nft.title}</h2>
                    <div className="flex w-650px">
                        <h3 className="text-white">ID:&nbsp;&nbsp;</h3> 
                        <h3>{nft.id.tokenId}</h3>
                    </div>
                    <div className="flex w-650px" >
                        <h3 className="text-white">Address:&nbsp;&nbsp;</h3> 
                        <h3>{nft.contract.address}</h3>
                    </div>
                    <div className="flex justify-start">
                        <div className="text-white">Description:&nbsp;&nbsp;</div>
                        {nft.description ? (<textarea className="w-80 h-20 overflow-clip overflow-y-auto">{nft.description}</textarea>):(<div>None</div>)} 
                    </div>
            </div>
        </div>
    )
}
```

#### &#x20;       9. 点击tailwind.config.js ，复制以下内容替换（记得保存）

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      skew: {
        '20': '20deg',
        '25': '25deg',
        '60': '60deg',
      }
    },
  },
  plugins: [],
}
```

#### &#x20;       10. 上传需要的图片

&#x20;           1）下载背景图片

&#x20;                  下图 右键-->图片另存为 ，名字修改为 <mark style="color:red;">back.jpg</mark>

<figure><img src="../.gitbook/assets/back.jpg" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_也可以使用自己的图片，只是文件名一定要改成上面的 back.jpg_

_若懂代码，可直接修改代码中的图片名。_

_图片可去_ [_https://www.pexels.com/zh-cn/_](https://www.pexels.com/zh-cn/) _上寻找_
{% endhint %}

&#x20;           2）左边栏点击Files右边3点，选 Upload file

&#x20;                                          ![](<../.gitbook/assets/image (61).png>)

&#x20;           3）选择刚才下载的back.jpg上传

&#x20;                                           ![](<../.gitbook/assets/image (3).png>)

&#x20;           4）将back.jpg拖拽到hymngallery/public目录中即可

&#x20;                                            ![](<../.gitbook/assets/image (94).png>)

&#x20;                                           ![](<../.gitbook/assets/image (36).png>)



### 三、运行项目

#### &#x20;       1. 点击Files右边三个点处，选择“show hidden files”

&#x20;                                           ![](<../.gitbook/assets/image (2).png>)

#### &#x20;       2. 点击.replit，复制以下内容替换（记得保存）

```
run = 'npm --prefix ./hymngallery run dev'
entrypoint = './hymngallery/pages/index.jsx'
```

{% hint style="info" %}
_如果最开始创建的目录名是自定义的，这里的 hymngallery 同样要改成之前自定义的名字_
{% endhint %}

#### &#x20;       3. 点击最顶部的 RUN

<figure><img src="../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

&#x20;           然后出现下图即正常运行

<figure><img src="../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

&#x20;           右上会显示

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



### 四、测试项目

#### &#x20;       1. 在replit出现页面示例的那里，会有一个链接，复制它并粘贴到新的网页上打开

<figure><img src="../.gitbook/assets/image (2) (4).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       2. 复制钱包地址，粘贴到第一个框里，点击 Show NFTs

<figure><img src="../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 显示

&#x20;         1）若在代码对应的网络上，此钱包里有NFT，会显示出来

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

&#x20;           2）如果没有NFT

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>



### 五、上传到Github

#### &#x20;       参照 [<mark style="color:red;">任务中所用平台和工具</mark>](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) <mark style="color:red;"></mark> 中 replit 如何提交到Github的操作流程



### 六、replit发布

#### &#x20;       参照 [<mark style="color:red;">任务中所用平台和工具</mark>](../preparation/ren-wu-zhong-suo-yong-ping-tai-he-gong-ju.md) <mark style="color:red;"></mark> 中 replit 如何发布的操作流程，记录发布地址

{% hint style="info" %}
_本周因未用到私钥，可以发布出来_
{% endhint %}



### 七、提交

#### &#x20;       本周能证明完成任务的地址

&#x20;           1）github地址

&#x20;           2）replit模板发布地址（publish生成后的地址）

&#x20;           3）replit构建运行的Dapp地址（run产生的地址，后缀是repl.co）

{% hint style="info" %}
_点_ [_<mark style="color:red;">此处</mark>_](../preparation/ren-wu-ti-jiao-ren-wu-wan-cheng-shi-kan.md) _到任务提交部分_
{% endhint %}



