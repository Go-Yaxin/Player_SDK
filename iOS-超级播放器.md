
腾讯云视立方 iOS 超级播放器是一款功能完整的播放器，集质量监控、视频加密、极速高清、清晰度切换、小窗播放等功能于一体，适用于所有点播、直播播放场景。

超级播放器封装了完整的功能并提供上层UI，简单几行代码即可拥有类似腾讯视频强大的播放功能。若超级播放器不满足您业务的个性化需求，且您具有一定的开发经验，可以集成 [播放器 SDK](https://cloud.tencent.com/document/product/881/20216)，自定义开发播放器界面和播放功能。




## 版本支持
腾讯云视立方 iOS 超级播放器是腾讯云视立方·音视频终端引擎的重要组成，本页文档所描述功能，在腾讯云视立方中支持情况如下：

| 版本名称 | 基础直播 Smart | 互动直播 Live | 短视频 UGSV | 音视频通话 TRTC | 播放器 Player | 全功能 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 支持情况 | -  | -  | -  | -  | &#10003;  | &#10003; |
| SDK 下载 <div style="width: 90px"/> | [下载](https://vcube.cloud.tencent.com/home.html?sdk=basicLive) | [下载](https://vcube.cloud.tencent.com/home.html?sdk=interactivelive) | [下载](https://vcube.cloud.tencent.com/home.html?sdk=shortVideo) | [下载](https://vcube.cloud.tencent.com/home.html?sdk=video) | [下载](https://vcube.cloud.tencent.com/home.html?sdk=player) | [下载](https://vcube.cloud.tencent.com/home.html?sdk=allPart) |

腾讯云视立方不同版本 SDK 包含的更多能力，具体请参见 [腾讯云视立方 SDK 下载](https://cloud.tencent.com/document/product/1449/56978)。


## 准备工作
1. 为了您体验到更完整全面的播放器功能，建议您开通 [云点播](https://cloud.tencent.com/product/vod) 相关服务，未注册用户可注册账号 [试用](https://cloud.tencent.com/login)。若您不使用云点播服务，可略过此步骤，但集成后仅可使用播放器基础能力。
2. 下载 Xcode，如您已下载可略过该步骤，您可以进入 App Store 下载安装。
3. 下载 Cocoapods，如您已下载可略过该步骤，您可以进入 [Cocoapods官网](https://cocoapods.org/) 按照指引进行安装。

## 通过本文您可以学会
- 如何集成腾讯云视立方 iOS 超级播放器
- 如何创建和使用播放器

## 集成准备
### 步骤1：项目下载
腾讯云视立方 iOS 超级播放器的项目地址是 [SuperPlayer_iOS](https://github.com/tencentyun/SuperPlayer_iOS)。

您可通过 **[下载播放器组件 ZIP 包](#zip)** 或  **[Git 命令下载](#git)** 的方式下载腾讯云视立方 iOS 超级播放器项目工程。
<dx-tabs>
::: 下载播放器组件 ZIP 包[](id:zip)
您可以直接下面播放器组件 ZIP包，单击页面的**Code**>**Download ZIP**下载。
![](https://qcloudimg.tencent-cloud.cn/raw/a38a9995bfe13d645bcd1d2e5242a297.png)
:::
::: Git 命令下载[](id:git)
1. 首先确认您的电脑上安装了 Git，如果没有安装，可以参见 [Git 安装教程](https://git-scm.com/downloads) 进行安装。
2. 执行下面的命令把超级播放器组件工程代码 clone 到本地。
   ```shell
   git clone git@github.com:tencentyun/SuperPlayer_iOS.git
   ```
   提示下面的信息表示成功 clone 工程代码到本地。
   ```shell
   正克隆到 'SuperPlayer_iOS'...
   remote: Enumerating objects: 2637, done.
   remote: Counting objects: 100% (644/644), done.
   remote: Compressing objects: 100% (333/333), done.
   remote: Total 2637 (delta 227), reused 524 (delta 170), pack-reused 1993
   接收对象中: 100% (2637/2637), 571.20 MiB | 3.94 MiB/s, 完成.
   处理 delta 中: 100% (1019/1019), 完成.
   ```

下载工程后，工程源码解压后的目录如下：

   | 文件名          | 作用                        |
   | --------------- | --------------------------- |
   | SDK             | 存放播放器的 framework 静态库 |
   | Demo            | 存放超级播放器 Demo          |
   | App             | 程序入口界面                |
   | SuperPlayerDemo | 超级播放器 Demo             |
   | SuperPlayerKit  | 超级播放器组件              |
:::
</dx-tabs>

### 步骤2：集成指引

本步骤，用于指导用户如何集成播放器，推荐用户选择使用 **[Cocoapods 集成](#cocoapods)** 或者 **[手动下载 SDK](#manual)** 再将其导入到您当前的工程项目中。
<dx-tabs>
::: Cocoapods 集成[](id:cocoapods)
1. 本项目支持 Cocoapods 安装，只需要将如下代码添加到 Podfile 中：
   ```xml
   pod 'SuperPlayer'
   ```
2. 执行 `pod install` 或 `pod update`。
:::
::: 手动下载 SDK[](id:manual)
1. 下载 SDK + Demo 开发包，腾讯云视立方 iOS 超级播放器项目为 [SuperPlayer_iOS](https://github.com/tencentyun/SuperPlayer_iOS)。
2. 导入`TXLiteAVSDK_Player.framework`到工程中，并勾选`Do Not Embed`。
:::
</dx-tabs>

### 步骤3：使用播放功能
本步骤，用于指导用户创建和使用播放器，并使用播放器进行视频播放。

#### 创建播放器[](id:usePlayer)
播放器主类为`SuperPlayerView`，创建后即可播放视频。
```xml
// 引入头文件
#import <SuperPlayer/SuperPlayer.h>

// 创建播放器  
_playerView = [[SuperPlayerView alloc] init];
// 设置代理，用于接受事件
_playerView.delegate = self;
// 设置父 View，_playerView 会被自动添加到 holderView 下面
_playerView.fatherView = self.holderView;
```
#### 播放视频
本步骤，用于指导用户播放视频，腾讯云视立方 iOS 超级播放器支持 [云点播 fileId](#fileid) 或者 [使用 url](#url) 进行播放，推荐您选择 **[集成 fileid](#fileid)** 使用更完善的能力。

<dx-tabs>
::: 云点播 fileId 播放[](id:fileid)
视频 FileId 在一般是在视频上传后，由服务器返回：

1. 客户端视频发布后，服务器会返回 FileId 到客户端。
2. 服务端视频上传时，在 [确认上传](https://cloud.tencent.com/document/product/266/9757) 的通知中包含对应的 FileId。

如果文件已存在腾讯云，则可以进入 [媒资管理](https://console.cloud.tencent.com/vod/media) ，找到对应的文件，查看 FileId。如下图所示，ID 即表示 FileId：
![](https://main.qcloudimg.com/raw/1a3677d5fe618227a117d7502be42793.png)
<dx-codeblock>
:::  java
SuperPlayerModel *model = [[SuperPlayerModel alloc] init];
model.appId = 1400329071;// 配置 AppId
model.videoId = [[SuperPlayerVideoId alloc] init];
model.videoId.fileId = @"5285890799710173650"; // 配置 FileId
//私有加密播放需填写 psign， psign 即超级播放器签名，签名介绍和生成方式参见链接：https://cloud.tencent.com/document/product/266/42436
//model.videoId.pSign = @"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhcHBJZCI6MTQwMDMyOTA3MSwiZmlsZUlkIjoiNTI4NTg5MDc5OTcxMDE3MzY1MCIsImN1cnJlbnRUaW1lU3RhbXAiOjEsImV4cGlyZVRpbWVTdGFtcCI6MjE0NzQ4MzY0NywidXJsQWNjZXNzSW5mbyI6eyJ0IjoiN2ZmZmZmZmYifSwiZHJtTGljZW5zZUluZm8iOnsiZXhwaXJlVGltZVN0YW1wIjoyMTQ3NDgzNjQ3fX0.yJxpnQ2Evp5KZQFfuBBK05BoPpQAzYAWo6liXws-LzU"; 
[_playerView playWithModel:model];
:::
</dx-codeblock>

:::
::: 使用 url 播放[](id:url)
```java
SuperPlayerModel *model = [[SuperPlayerModel alloc] init];
model.videoURL = @"http://your_video_url.mp4";   // 配置您的播放视频url
[_playerView playWithModel:model];
```
:::
</dx-tabs>

#### 退出播放[](id:exitPlayer)
当不需要播放器时，调用`resetPlayer`清理播放器内部状态，释放内存。

```java
[_playerView resetPlayer];
```
 
您已完成了腾讯云视立方 iOS 超级播放器的创建、播放视频和退出播放的能力集成。

## 更多功能[](id:moreFeature)
完整功能可扫码下载视频云工具包体验，或直接运行工程 Demo。

#### 一、如何运行工程 Demo
1. 在 Demo 目录，执行命令行 pod update，重新生成 TXLiteAVDemo.xcworkspace 文件。
2. 双击打开工程，修改证书选择真机运行。
3. 成功运行 Demo 后，进入**播放器**> **超级播放器**，可体验播放器功能。

#### 二、视频云工具包
<img src="https://main.qcloudimg.com/raw/12c7da97cc910eda673cb19b66fc7cb3.png" width="150">



