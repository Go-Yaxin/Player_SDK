## 准备工作
1、

2、

3、

## 通过本文你可以学会
* 如何集成腾讯云视立方iOS播放器SDK
* 如何使用播放器SDK进行点播播放（包含启动播放、结束播放）
* 如何使用播放器SDK底层能力实现更多功能

## SDK集成
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
（原步把骤12，替换为下面内容）
播放器SDK支持在界面添加图片贴片，用于广告宣传等。实现方式如下：
* 将autoPlay 为 NO，此时播放器会正常加载，但视频不会立刻开始播放。
* 在播放器加载出来后、视频尚未开始时，在播放器界面添加图片广告贴片。
* 待达到广告展示结束条件时，使用resume函数启动视频播放。

### 7、HTTP-REF（UGSV暂不支持）
（原步骤14）
### 8、硬件加速
（原步骤15）
### 9、多码率文件
（原步骤16）
### 10、码流自适应
（原步骤17，替换为下面内容）

SDK 支持 HLS 的多码流自适应，开启相关能力后播放器能够根据当前带宽，动态选择最合适的码率播放。在收到 PLAY_EVT_PLAY_BEGIN 事件后，可以通过下面方法开启码流自适应：
```objectivec
[_txVodPlayer setBitrateIndex:-1]; //index 参数传入-1
```
在播放过程中，可以随时通过 `-[TXVodPlayer setBitrateIndex:]` 切换其它码率，切换后码流自适应也随之关闭。

### 11、加密播放（UGSV版本暂不支持）
（原步骤13）
视频加密方案可用于在线教育、独播剧等需要对视频版权进行保护的场景。完整的视频加密方案需要首先对视频资源进行加密保护，随后基于播放器实现加密视频播放，
具体实现方式参见[播放加密视频](https://cloud.tencent.com/document/product/266/46220)。

目前 TXVodPlayer 同样支持加密播放，您可以使用通过 [URL](https://cloud.tencent.com/document/product/266/9638#.3Ca-id.3D.22p1.22.3E.3C.2Fa.3E.E8.A7.86.E9.A2.91.E6.92.AD.E6.94.BE.E6.96.B9.E6.A1.881) 携带身份认证信息的方案，该种方案下 SDK 的调用方式跟普通情况没有什么区别。 您也可以使用 [Cookie](https://cloud.tencent.com/document/product/266/9638#cookie) 携带身份认证信息的方案，该种方案下，需要您通过 TXVodPlayConfig 中的 headers 字段设置 cookie 信息于 HTTP 请求头中。


### 12、本地缓存
（原步骤10）

### 13、离线缓存
（原离线下载）

离线缓存支持用户在有网络的条件下缓存视频，随后在无网络的环境下观看。同时播放器SDK提供本地加密能力，离线缓存后的本地视频仍为加密状态，仅可通过指定播放器对视频进行解密播放，可有效防止缓存视频的非法传播，保护视频安全。

由于HLS 流媒体无法直接保存到本地，因此也无法通过播放本地文件的方式实现HLS离线播放，对于该问题，您可以通过基于`TXVodDownloadManager`的离线缓存方案实现HLS的离线播放。

> ! `TXVodDownloadManager` 暂不支持缓存 MP4 和 FLV 格式的文件，仅支持缓存 HLS 格式文件。
> ! 播放器 SDK 已支持 MP4 和 FLV 格式的本地文件播放。

[](id:offline1)
#### 步骤1：准备工作

`TXVodDownloadManager`被设计为单例，因此您不能创建多个下载对象。用法如下：

```objective-c
TXVodDownloadManager *downloader = [TXVodDownloadManager shareInstance];
[downloader setDownloadPath:"<指定您的下载目录>"];
```

[](id:offline2)
#### 步骤2：开始下载

开始下载有两种方式：URL 和 fileid。
- **URL 方式：**只需要传入下载地址即可。
```objective-c
[downloader startDownloadUrl:@"http://1253131631.vod2.myqcloud.com/26f327f9vodgzp1253131631/f4bdff799031868222924043041/playlist.m3u8"]
```
- **fileid 方式：**fileid 下载至少需要传入 appId 和 fileId。
```objective-c
TXPlayerAuthParams *auth = [TXPlayerAuthParams new];
auth.appId = 1252463788;
auth.fileId = @"4564972819220421305";
TXVodDownloadDataSource *dataSource = [TXVodDownloadDataSource new];
dataSource.auth = auth;
[downloader startDownload:dataSource];
```


[](id:offline3)
#### 步骤3：任务信息 

在接收任务信息前，需要先设置回调 delegate。

```objective-c
downloader.delegate = self;
```

可能收到的任务回调有：
<table><thead>
<tr><th width="55%">回调信息</th><th>含义</th></tr>
</thead>
<tbody><tr>
<td>-[TXVodDownloadDelegate onDownloadStart:]</td>
<td>任务开始，表示 SDK 已经开始下载</td>
</tr>
<tr>
<td>-[TXVodDownloadDelegate onDownloadProgress:]</td>
<td>任务进度，下载过程中，SDK 会频繁回调此接口，您可以在这里更新进度显示</td>
</tr>
<tr>
<td>-[TXVodDownloadDelegate onDownloadStop:]</td>
<td>任务停止，当您调用是<code>stopDownload</code>停止下载，收到此消息表示停止成功</td>
</tr>
<tr>
<td>-[TXVodDownloadDelegate onDownloadFinish:]</td>
<td>下载完成，收到此回调表示已全部下载。此时下载文件可以给 TXVodPlayer 播放</td>
</tr>
<tr>
<td>-[TXVodDownloadDelegate onDownloadError:errorMsg:]</td>
<td>下载错误，下载过程中遇到网络断开会回调此接口，同时下载任务停止。所有错误码请参考<code>TXDownloadError</code></td>
</tr>
</tbody></table>

由于 downloader 可以同时下载多个任务，所以回调接口里带上了`TXVodDownloadMediaInfo`对象，您可以访问 URL 或 dataSource 判断下载源，同时还可以获取到下载进度、文件大小等信息。

[](id:offline4)
#### 步骤4：中断下载

停止下载请调用 `-[TXVodDownloadManager stopDownload:]` 方法，参数为 `-[TXVodDownloadManager sartDownloadUrl:]` 返回的对象。**SDK 支持断点续传**，当下载目录没有发生改变时，下次下载同一个文件时会从上次停止的地方重新开始。

如果您不需要重新下载，请调用 `-[TXVodDownloadManager deleteDownloadFile:]` 方法删除文件，以释放存储空间。

### 14、进度展示
（原进度展示，只改变原来的第一句话，其他不变）
点播播放中的进度信息分为2种：**加载进度**和**播放进度**，SDK 目前是以事件通知的方式将这两个进度实时通知出来的。更多事件通知内容参见[事件监听](下面的标题)




## 事件监听
您可以为 TXVodPlayer 对象绑定一个 TXVodPlayListener 监听器，即可通过onPlayEvent（事件通知） 和 onNetStatus（状态反馈）向您的应用程序同步信息。

### 事件通知（onPlayEvent）

[](id:listen_play)
#### 播放事件
| 事件 ID               |   数值  |  含义说明                 |
| :-------------------  |:-------- |  :------------------------ |
|PLAY_EVT_PLAY_BEGIN    |  2004|  视频播放开始，此时用户可见视频首帧 |
|PLAY_EVT_PLAY_PROGRESS |  2005|  视频播放进度，会通知当前播放进度、加载进度和总体时长     |
|PLAY_EVT_PLAY_LOADING  |  2007|  视频播放过程中出现暂停加载现象，即loading，如果能够恢复，之后会有 LOADING_END 事件|
|PLAY_EVT_VOD_LOADING_END   |  2014|  视频播放过程中的暂停加载结束，即loading结束，视频继续播放|
|PLAY_EVT_GET_PLAYINFO_SUCC   |  |  |




[](id:listen_stop)
#### 结束事件
| 事件 ID                 |    数值  |  含义说明                |
| :-------------------  |:-------- |  :------------------------ |
|PLAY_EVT_PLAY_END      |  2006|  视频播放结束   |
|PLAY_ERR_NET_DISCONNECT |  -2301  |  网络断连，且经多次重连亦不能恢复，更多重试请自行重启播放 |
|PLAY_ERR_HLS_KEY       | -2305 | HLS 解密 key 获取失败 |

[](id:listen_warning)
#### 警告事件
如下的这些事件您可以不用关心，它只是用来告知您 SDK 内部的一些事件。

| 事件 ID                 |    数值  |  含义说明                    |
| :-------------------  |:-------- |  :------------------------ |
| PLAY_WARNING_VIDEO_DECODE_FAIL   |  2101  | 当前视频帧解码失败  |
| PLAY_WARNING_AUDIO_DECODE_FAIL   |  2102  | 当前音频帧解码失败  |
| PLAY_WARNING_RECONNECT           |  2103  | 网络断连，已启动自动重连（重连超过三次就直接抛送 PLAY_ERR_NET_DISCONNECT 了）|
| PLAY_WARNING_HW_ACCELERATION_FAIL|  2106  | 硬解启动失败，采用软解   |

[](id:listen_conect)
### 连接事件
此外还有几个连接服务器的事件，主要用于测定和统计服务器连接时间，您也无需关心：

| 事件 ID                     |    数值  |  含义说明                    |
| :-----------------------  |:-------- |  :------------------------ |
| PLAY_EVT_VOD_PLAY_PREPARED     |  2013    | 播放器已准备完成，可以播放。设置了 autoPlay 为 false 之后，需要在收到此事件后，调用 resume 才会开始播放     |
| PLAY_EVT_RCV_FIRST_I_FRAME|  2003    | 网络接收到首个可渲染的视频数据包（IDR）  |

[](id:listen_change)
#### 分辨率事件
以下事件用于获取画面变化信息，您也无需关心：

| 事件 ID                     |    数值  |  含义说明                    |
| :-----------------------  |:-------- |  :------------------------ |
| PLAY_EVT_CHANGE_RESOLUTION|  2009    | 视频分辨率改变               |
| PLAY_EVT_CHANGE_ROATION   |  2011    | MP4 视频旋转角度 |

[](id:videoWH)
#### 状态反馈（onNetStatus）
![image](https://user-images.githubusercontent.com/88317062/145408405-c0c43180-298a-4089-9340-e0cc0319937d.png)

