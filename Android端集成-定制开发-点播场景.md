## 准备工作
1. 开通 [云点播](https://cloud.tencent.com/product/vod) 相关服务，未注册用户可注册账号 [试用](https://cloud.tencent.com/login)。
2. 下载 Android Studio，您可以进入 [Android Studio 官网](https://developer.android.com/studio)下载安装，如已下载可略过该步骤。

## 通过本文你可以学会
* 如何集成腾讯云视立方Android播放器SDK
* 如何使用播放器SDK进行点播播放
* 如何使用播放器SDK底层能力实现更多功能

## SDK集成
[](id:step1)

### 步骤1：下载 SDK 开发包

[下载](https://vcube.cloud.tencent.com/home.html) SDK 开发包，并按照 [SDK 集成指引](https://cloud.tencent.com/document/product/1449/56987) 将 SDK 嵌入您的 App 工程中。

[](id:step2)

### 步骤2：添加 View

为了能够展示播放器的视频画面，我们第一步要做的就是在布局 xml 文件里加入如下一段代码：

```xml
<com.tencent.rtmp.ui.TXCloudVideoView
            android:id="@+id/video_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_centerInParent="true"
            android:visibility="gone"/>
```

[](id:step3)

### 步骤3：创建 Player

接下来创建一个 **TXVodPlayer** 的对象，并使用 setPlayerView 接口将它与我们刚添加到界面上的 **video_view** 控件进行关联。

```java
//mPlayerView 即步骤1中添加的界面 view
TXCloudVideoView mView = (TXCloudVideoView) view.findViewById(R.id.video_view);
//创建 player 对象
TXVodPlayer mVodPlayer = new TXVodPlayer(getActivity());
//关联 player 对象与界面 view
mVodPlayer.setPlayerView(mView);
```

[](id:step4)

### 步骤4：启动播放

TXVodPlayer 支持两种播放模式，您可以根据需要自行选择：

- **通过 URL 方式：**
  TXVodPlayer 内部会自动识别播放协议，您只需要将您的播放 URL 传给 startPlay 函数即可。

```java
String url = "http://1252463788.vod2.myqcloud.com/xxxxx/v.f20.mp4";
mVodPlayer.startPlay(url); 
```

- **通过 FileId 方式：**

```objectivec
TXPlayerAuthBuilder authBuilder = new TXPlayerAuthBuilder();
authBuilder.setAppId(1252463788);
authBuilder.setFileId("4564972819220421305");
mVodPlayer.startPlay(authBuilder); 
```

在 [媒资管理](https://console.cloud.tencent.com/vod/media) 找到对应的视频文件。在文件名下方可以看到 FileId。

通过 FileId 方式播放，播放器会向后台请求真实的播放地址。如果此时网络异常或 FileId 不存在，则会收到`TXLiveConstants.PLAY_ERR_GET_PLAYINFO_FAIL`事件，反之收到`TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC`表示请求成功。

[](id:5)

### 步骤5：结束播放

结束播放时**记得销毁 view 控件**，尤其是在下次 startPlay 之前，否则会产生大量的内存泄露以及闪屏问题。

同时，在退出播放界面时，记得一定要调用渲染 View 的`onDestroy()`函数，否则可能会产生内存泄露和“Receiver not registered”报警。

```java
@Override
public void onDestroy() {
    super.onDestroy();
    mVodPlayer.stopPlay(true); // true 代表清除最后一帧画面
    mView.onDestroy(); 
}
```

stopPlay 的布尔型参数含义为—— “是否清除最后一帧画面”。早期版本的 RTMP SDK 的直播播放器没有 pause 的概念，所以通过这个布尔值来控制最后一帧画面的清除。

如果是点播播放结束后，也想保留最后一帧画面，您可以在收到播放结束事件后什么也不做，默认停在最后一帧。

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
* 待达到广告展示结束条件时，使用resume接口启动视频播放。

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


### 11、加密播放
视频加密方案主要用于在线教育等需要对视频版权进行保护的场景。如果要对您的视频资源进行加密保护，就不仅需要在播放器上做改造，还需要对视频源本身进行加密转码，亦需要您的后台和终端研发工程师都参与其中。在 [视频加密解决方案](https://cloud.tencent.com/document/product/266/45552) 中您会了解到全部细节内容。


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
|TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC   | 2010 | 成功获取播放文件信息 |


（原视频信息）
如果通过 fileId 方式播放且请求成功，SDK 会将一些请求信息通知到上层。您可以在收到`TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC`事件后，解析 param 获取视频信息。
![image](https://user-images.githubusercontent.com/88317062/145537652-5b4a7f2e-4586-4ee4-9591-d13e876df33f.png)


### 状态反馈（onNetStatus）
（原onNetStatus（状态反馈））
状态反馈每0.5秒都会被触发一次，目的是实时反馈当前的推流器状态，它就像汽车的仪表盘，可以告知您目前 SDK 内部的一些具体情况，以便您能对当前视频播放状态等有所了解。
![image](https://user-images.githubusercontent.com/88317062/145537692-86679b06-7b66-4abd-b913-a7af8df7e005.png)
