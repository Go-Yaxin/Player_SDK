## 准备工作——dokie
1. 开通 [云点播](https://cloud.tencent.com/product/vod) 相关服务，未注册用户可注册账号 [试用](https://cloud.tencent.com/login)。
2. 下载 Xcode，如您已下载可略过该步骤，您可以进入 App Store 下载安装。
3. 下载 Cocoapods，如您已下载可略过该步骤，您可以进入 [Cocoapods官网](https://cocoapods.org/) 按照指引进行安装。

## 通过本文你可以学会
* 如何集成腾讯云视立方iOS播放器SDK
* 如何使用播放器SDK进行点播播放
* 如何使用播放器SDK底层能力实现更多功能

## SDK集成——dokie
[](id:step1)

### 步骤1：下载 SDK 开发包

[下载](https://vcube.cloud.tencent.com/home.html) SDK 开发包，并按照 [SDK 集成指引](https://cloud.tencent.com/document/product/1449/56986) 将 SDK 嵌入您的 App 工程中。

[](id:step2)

### 步骤2：创建 Player

视频云 SDK 中的 TXVodPlayer 模块负责实现点播播放功能。

```objectivec
TXVodPlayer *_txVodPlayer = [[TXVodPlayer alloc] init];
[_txVodPlayer setupVideoWidget:_myView insertIndex:0]
```

[](id:step3)

### 步骤3：渲染 View

接下来我们要给播放器的视频画面找个地方来显示，iOS 系统中使用 view 作为基本的界面渲染单位，所以您只需要准备一个 view 并调整好布局就可以了。

```objectivec
[_txVodPlayer setupVideoWidget:_myView insertIndex:0]
```

内部原理上讲，播放器并不是直接把画面渲染到您提供的 view（示例代码中的 \_myView）上，而是在这个 view 之上创建一个用于 OpenGL 渲染的子视图（subView）。

如果您要调整渲染画面的大小，只需要调整您所常见的 view 的大小和位置即可，SDK 会让视频画面跟着您的 view 的大小和位置进行实时的调整。

![](https://main.qcloudimg.com/raw/3c15f622f653f87fa49b3c1ee1ebc173.png)

**如何做动画**
 针对 view 做动画是比较自由的，不过请注意此处动画所修改的目标属性应该是 transform 属性而不是 frame 属性。

```objectivec
  [UIView animateWithDuration:0.5 animations:^{
            _myView.transform = CGAffineTransformMakeScale(0.3, 0.3); // 缩小1/3
        }];
```

[](id:step4)

### 步骤4：启动播放

TXVodPlayer 支持两种播放模式，您可以根据需要自行选择：

- **通过 URL 方式：**
  TXVodPlayer 内部会自动识别播放协议，您只需要将您的播放 URL 传给 startPlay 函数即可。

```objectivec
NSString* url = @"http://1252463788.vod2.myqcloud.com/xxxxx/v.f20.mp4";
[_txVodPlayer startPlay:url ];
```

- **通过 fileId 方式：**

```objectivec
TXPlayerAuthParams *p = [TXPlayerAuthParams new];
p.appId = 1252463788;
p.fileId = @"4564972819220421305";
[_txVodPlayer startPlayWithParams:p];
```

在 [媒资管理](https://console.cloud.tencent.com/vod/media) 找到对应的文件。点开后在右侧视频详情中，可以看到 fileId。
通过 fileId 方式播放，播放器会向后台请求真实的播放地址。如果此时网络异常或 fileId 不存在，则会收到`PLAY_ERR_GET_PLAYINFO_FAIL`事件，反之收到`PLAY_EVT_GET_PLAYINFO_SUCC`表示请求成功。

### 步骤7：结束播放

结束播放时，如果要退出当前的 UI 界面，要记得用 **removeVideoWidget** 销毁 view 控件，否则会产生内存泄露或闪屏问题。

```objectivec
// 停止播放
[_txVodPlayer stopPlay];
[_txVodPlayer removeVideoWidget]; // 记得销毁 view 控件
```

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

### 7、HTTP-REF（UGC版本暂不支持）
（原步骤14）
### 8、硬件加速
（原步骤15）
### 9、多码率文件（UGC版本暂不支持）
（原步骤16）
### 10、码流自适应
（原步骤17，替换为下面内容）

SDK 支持 HLS 的多码流自适应，开启相关能力后播放器能够根据当前带宽，动态选择最合适的码率播放。在收到 `PLAY_EVT_PLAY_BEGIN` 事件后，可以通过下面方法开启码流自适应：
```objectivec
[_txVodPlayer setBitrateIndex:-1]; //index 参数传入-1
```
在播放过程中，可以随时通过 `-[TXVodPlayer setBitrateIndex:]` 切换其它码率，切换后码流自适应也随之关闭。

### 11、加密播放（UGC版本暂不支持）——dokie
（原步骤13。内容待补充）
视频加密方案主要用于在线教育等需要对视频版权进行保护的场景。如果要对您的视频资源进行加密保护，就不仅需要在播放器上做改造，还需要对视频源本身进行加密转码，亦需要您的后台和终端研发工程师都参与其中。在 [视频加密解决方案](https://cloud.tencent.com/document/product/266/45552) 中您会了解到全部细节内容。


### 12、本地缓存（UGC版本暂不支持）
（原步骤10）

### 13、离线缓存
（原离线下载，首段描述内容变更，截图内容不变）

离线缓存支持用户在有网络的条件下缓存视频，随后在无网络的环境下观看。同时播放器SDK提供本地加密能力，离线缓存后的本地视频仍为加密状态，仅可通过指定播放器对视频进行解密播放，可有效防止缓存视频的非法传播，保护视频安全。

由于HLS 流媒体无法直接保存到本地，因此也无法通过播放本地文件的方式实现HLS离线播放，对于该问题，您可以通过基于`TXVodDownloadManager`的离线缓存方案实现HLS的离线播放。

> ! `TXVodDownloadManager` 暂不支持缓存 MP4 和 FLV 格式的文件，仅支持缓存 HLS 格式文件。
> 
> ! 播放器 SDK 已支持 MP4 和 FLV 格式的本地文件播放。

![image](https://user-images.githubusercontent.com/88317062/145514645-30ff4f60-ac00-42fe-964a-361c81207ee2.png)


### 14、进度展示
（原进度展示，只改变原来的第一句话，其他不变）
点播播放中的进度信息分为2种：**加载进度**和**播放进度**，SDK 目前是以事件通知的方式将这两个进度实时通知出来的。更多事件通知内容参见[事件监听](下面的标题)。
![image](https://user-images.githubusercontent.com/88317062/145514735-6b5fb789-ddd2-4b72-97f8-1d426d74bb60.png)


### 15、网速展示
通过[事件监听](下面的标题)方式，可以在视频播放卡顿时在显示当前网速。
* 通过`onNetStatus`的`NET_SPEED`获取当前网速。具体使用方法见[状态反馈（onNetStatus）](下面的标题)。
* 监听到`PLAY_EVT_PLAY_LOADING`事件后，显示当前网速。
* 收到`PLAY_EVT_VOD_LOADING_END`事件后，对显示当前网速的 view 进行隐藏。

### 16、获取视频信息
播放器SDK通过URL字符串播放视频，URL中本身不包含视频信息。为获取相关信息，需要通过访问云端服务器加载到相关视频信息，因此SDK 只能以事件通知的方式将视频信息发送到您的应用程序中，更多内容参见[事件监听](下面的标题)。

**分辨率信息**

方法1：通过`onNetStatus`的`VIDEO_WIDTH`和`VIDEO_HEIGHT`获取视频的宽和高。具体使用方法见[状态反馈（onNetStatus）](下面的标题)。

方法2：直接调用`-[TXVodPlayer width]`和`-[TXVodPlayer height]`获取当前宽高。


## 事件监听
您可以为 TXVodPlayer 对象绑定一个 TXVodPlayListener 监听器，即可通过onPlayEvent（事件通知） 和 onNetStatus（状态反馈）向您的应用程序同步信息。

### 事件通知（onPlayEvent）

#### 播放事件
(原播放事件)
#### 结束事件
(原结束事件)
#### 警告事件
(原警告事件)
#### 连接事件
(原连接事件)
#### 画面事件
(原分辨率事件)

#### 视频信息事件——dokie
| 事件 ID                     |    数值  |  含义说明                    |
| :-----------------------  |:-------- |  :------------------------ |
|PLAY_EVT_GET_PLAYINFO_SUCC   | 2010 | 成功获取播放文件信息 |


（原视频信息）
如果通过 fileId 方式播放且请求成功，SDK 会将一些请求信息通知到上层。您可以在收到 PLAY_EVT_GET_PLAYINFO_SUCC 事件后，解析 param 获取视频信息。
![image](https://user-images.githubusercontent.com/88317062/145534673-cc63bfe4-22f0-4b25-9179-9e4a01b98d51.png)


### 状态反馈（onNetStatus）
（原onNetStatus（状态反馈））
状态反馈每0.5秒都会被触发一次，目的是实时反馈当前的推流器状态，它就像汽车的仪表盘，可以告知您目前 SDK 内部的一些具体情况，以便您能对当前视频播放状态等有所了解。
![image](https://user-images.githubusercontent.com/88317062/145514344-5feb8a0e-b91c-4f3c-87cd-8a4f78023f29.png)
