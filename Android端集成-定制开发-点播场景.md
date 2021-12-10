## 准备工作——dokie
1、

2、

3、

## 通过本文你可以学会
* 如何集成腾讯云视立方Android播放器SDK
* 如何使用播放器SDK进行点播播放
* 如何使用播放器SDK底层能力实现更多功能

## SDK集成——dokie
（包含集成、启动播放和结束播放）

步骤1:

步骤2:

步骤3:

## 功能使用
### 1、播放控制
（原步骤6）

### 2、画面调整
（原步骤5）

### 3、变速播放
（原步骤9）

### 4、屏幕截图
（原步骤8）

### 5、预加载
（原步骤11）

### 6、贴片广告
（原步骤12，替换为下面内容）
播放器SDK支持在界面添加图片贴片，用于广告宣传等。实现方式如下：
* 将`autoPlay`为 NO，此时播放器会正常加载，但视频不会立刻开始播放。
* 在播放器加载出来后、视频尚未开始时，即可在播放器界面查看图片贴片广告。
* 待达到广告展示结束条件时，使用resume函数启动视频播放。

### 7、HTTP-REF
（原步骤14）
### 8、硬件加速
（原步骤15）
### 9、多码率文件
（原步骤16）
### 10、码流自适应
（原步骤17，替换为下面内容）

SDK 支持 HLS 的多码流自适应，开启相关能力后播放器能够根据当前带宽，动态选择最合适的码率播放。在收到 PLAY_EVT_PLAY_BEGIN 事件后，可以通过下面方法开启码流自适应。
```java
mVodPlayer.setBitrateIndex(-1); //index 参数传入-1
```
在播放过程中，可以随时通过 `mVodPlayer.setBitrateIndex(int)` 切换其它码率，切换后码流自适应也随之关闭。


### 11、加密播放——dokie
（原步骤13。内容待补充）
视频加密方案可用于在线教育、独播剧等需要对视频版权进行保护的场景。完整的视频加密方案需要首先对视频资源进行加密保护，随后基于播放器实现加密视频播放，
具体实现方式参见[播放加密视频](https://cloud.tencent.com/document/product/266/46220)。


### 12、本地缓存
（原步骤10）

### 13、离线缓存
（原离线下载，首段描述内容变更，截图内容不变）

离线缓存支持用户在有网络的条件下缓存视频，随后在无网络的环境下观看。同时播放器SDK提供本地加密能力，离线缓存后的本地视频仍为加密状态，仅可通过指定播放器对视频进行解密播放，可有效防止缓存视频的非法传播，保护视频安全。

由于HLS 流媒体无法直接保存到本地，因此也无法通过播放本地文件的方式实现HLS离线播放，对于该问题，您可以通过基于`TXVodDownloadManager`的离线缓存方案实现HLS的离线播放。

> ! `TXVodDownloadManager` 暂不支持缓存 MP4 和 FLV 格式的文件，仅支持缓存 HLS 格式文件。
> 
> ! 播放器 SDK 已支持 MP4 和 FLV 格式的本地文件播放。

![image](https://user-images.githubusercontent.com/88317062/145535916-52eef391-1100-4ed9-8670-e350c1173286.png)


### 14、进度展示
（原进度展示，只改变原来的第一句话，其他不变）
点播播放中的进度信息分为2种：**加载进度**和**播放进度**，SDK 目前是以事件通知的方式将这两个进度实时通知出来的。更多事件通知内容参见[事件监听](下面的标题)。
![image](https://user-images.githubusercontent.com/88317062/145536750-4dfbb8f9-7738-4aa0-a0fa-68afd0df2a9b.png)


### 15、网速展示
通过[事件监听](下面的标题)方式，可以在视频播放卡顿时在显示当前网速。
* 通过`onNetStatus`的`NET_STATUS_NET_SPEED`获取当前网速。具体使用方法见[状态反馈（onNetStatus）](下面的标题)。
* 监听到`PLAY_EVT_PLAY_LOADING`事件后，显示当前网速。
* 收到`PLAY_EVT_VOD_LOADING_END`事件后，对显示当前网速的 view 进行隐藏。

### 16、获取视频信息
播放器SDK通过URL字符串播放视频，URL中本身不包含视频信息。为获取相关信息，需要通过访问云端服务器加载到相关视频信息，因此SDK 只能以事件通知的方式将视频信息发送到您的应用程序中，更多内容参见[事件监听](下面的标题)。

**分辨率信息**

方法1：通过`onNetStatus`的`NET_STATUS_VIDEO_WIDTH`和`NET_STATUS_VIDEO_HEIGHT`获取视频的宽和高。具体使用方法见[状态反馈（onNetStatus）](下面的标题)。

方法2：直接调用 `TXVodPlayer.getWidth()`和`TXVodPlayer.getHeight()` 获取当前宽高。


## 事件监听
您可以为 TXVodPlayer 对象绑定一个 TXVodPlayListener 监听器，即可通过onPlayEvent（事件通知） 和 onNetStatus（状态反馈）向您的应用程序同步信息。

### 事件通知（onPlayEvent）

#### 播放事件
(原播放事件，第一个做更改)
![image](https://user-images.githubusercontent.com/88317062/145537348-80f2d83a-815a-47fe-8cc2-c1f3475ded50.png)

#### 结束事件
(原结束事件)
#### 警告事件
(原警告事件)
#### 连接事件
(原连接事件)
![image](https://user-images.githubusercontent.com/88317062/145537432-cfb2cf4b-0ab7-46de-9c55-cdf15183c3a8.png)

#### 画面事件
(原分辨率事件)
![image](https://user-images.githubusercontent.com/88317062/145537472-29dc6a41-aaff-475b-a435-008fe4ec7682.png)

#### 视频信息事件——dokie
| 事件 ID                     |    数值  |  含义说明                    |
| :-----------------------  |:-------- |  :------------------------ |
|TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC   |  |  |


（原视频信息）
如果通过 fileId 方式播放且请求成功，SDK 会将一些请求信息通知到上层。您可以在收到`TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC`事件后，解析 param 获取视频信息。
![image](https://user-images.githubusercontent.com/88317062/145537652-5b4a7f2e-4586-4ee4-9591-d13e876df33f.png)


### 状态反馈（onNetStatus）
（原onNetStatus（状态反馈））
状态反馈每0.5秒都会被触发一次，目的是实时反馈当前的推流器状态，它就像汽车的仪表盘，可以告知您目前 SDK 内部的一些具体情况，以便您能对当前视频播放状态等有所了解。
![image](https://user-images.githubusercontent.com/88317062/145537692-86679b06-7b66-4abd-b913-a7af8df7e005.png)
