---
cover: ../.gitbook/assets/prepare-cover.jpg
coverY: -349.6317512274959
---

# 任务中所用平台和工具

### 一、在Alchemy上创建APP的流程

#### &#x20;       1. 进入 [https://dashboard.alchemy.com/](https://dashboard.alchemy.com/) ，注册（已注册的跳过此步）

{% hint style="warning" %}
_**注意事项：**_&#x20;

_a. 谷歌邮箱已无法注册_

_b. 建议注册2个账户，先进到其中一个账户APP界面等着_

_c. 如果一个账号已构建5个APP，请及时更换到另一个账户（此步流程可参考其他教程）_
{% endhint %}

#### &#x20;       2. 登录Alchemy账号，选中 dashboard ，点击 Create App

<figure><img src="../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 弹出页面上填写名字、选好网络等，点击create app

{% hint style="info" %}
NAME处根据任务来命名
{% endhint %}

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 创建成功后，点击 VIEW KEY

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       5. 复制两个红框的内容，<mark style="color:red;">HTTPS</mark> 和 <mark style="color:red;">API KEY</mark> ，用于任务

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>



### 二、钱包相关

* [x] **获得Metamask钱包私钥的方法**

#### &#x20;           **1. 点击 Metamask 右上角的三点，点击 账户详情**           &#x20;

&#x20;                                              ****                                               ![](<../.gitbook/assets/image (13).png>)

#### &#x20;           2. 点击 导出私钥

<figure><img src="../.gitbook/assets/image (15) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;           3. 复制红框中内容，用于任务

{% hint style="info" %}
_建议采用_ [_<mark style="color:red;">任务准备</mark>_](ren-wu-zhun-bei-zuo-qian-bi-kan.md) _中重要提醒里的小技巧_
{% endhint %}

<figure><img src="../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

* [x] **从Metamask钱包查询智能合约**

#### &#x20;           1. 点开 Metamask，选好 <mark style="color:red;">对应的网络</mark>，再点账号旁边3个点，点击 <mark style="color:red;">在资源浏览器中查看</mark>

&#x20;                                                    ![](<../.gitbook/assets/image (21).png>)

#### &#x20;           2. 如果区块浏览器的地址不对，就点击钱包网络旁边的按钮，选 设置（正常的话跳过此步）

&#x20;                                                    ![](<../.gitbook/assets/image (3).png>)

&#x20;               1）进去后选择 网络

&#x20;                                                    ![](<../.gitbook/assets/image (18) (1).png>)&#x20;

&#x20;               2）选择对应的网络，进行设置（比如Optimism Goerli）

&#x20;                                                    ![](<../.gitbook/assets/image (4) (2).png>)

&#x20;               3）修改区块浏览器地址后，点击 保存

&#x20;                                              ![](<../.gitbook/assets/image (2).png>)

#### &#x20;           3. 区块浏览器会在新网页打开，展示所有合约，默认最新的在前面

{% hint style="info" %}
_图中左上是钱包地址。 Contract Creation就是最新创建的合约，点进去就能看到最新合约所有的交互，第二个图红框处即合约地址_
{% endhint %}

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>



### 三、replit平台使用

* [x] **如何提交到Github**

#### &#x20;       1. 在具体项目页面，点击左边栏第二个 版本控制

&#x20;                                              ![](<../.gitbook/assets/image (26) (1).png>)

{% hint style="info" %}
_a. 需要等一小会，才会出现下拉菜单，选commit all & push_

_b. 必须填写中间红圈处的上传说明内容，才可点击commit all & push，否则会失败_
{% endhint %}

#### &#x20;       2. 会等待大约几分钟，才能上传到Github。只要显示没有新的改变了，就表示完成

&#x20;                                   ![](<../.gitbook/assets/image (17) (1).png>)

#### &#x20;       3. 完成后，可切换至Github上对应的repository页面，刷新即可看见最新上传的内容



* [x] **如何发布项目（慎用）**

{% hint style="warning" %}
_发布前切记删除一切和私钥相关的文件_
{% endhint %}

#### &#x20;       1. 在具体项目页面，右上角点击 publish

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
_也有朋友遇到不在右上角的情况，如下寻找即可_

![](<../.gitbook/assets/image (28).png>)
{% endhint %}

#### &#x20;       2.  弹出页面的第一页，红圈处必须填写才能进入下一页

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       3. 后面一路next到最后一页，勾上 做成模板，点击 publish

<figure><img src="../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8) (2).png" alt=""><figcaption></figcaption></figure>

#### &#x20;       4. 最后弹出页面，记录下红框处地址，可用于任务提交

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

