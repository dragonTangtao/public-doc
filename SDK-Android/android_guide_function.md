#羚羊云Android SDK功能概要
##1. 概述
本SDK可供Android平台下的应用调用，为开发者提供接入羚羊视频云的开发接口，使开发者能够轻松实现视频相关的应用。羚羊视频云在视频传输和云存储领域有着领先的开发技术和丰富的产品经验,设计了高质量、宽适应性、分布式、模块化的音视频传输和存储云平台。SDK为上层应用提供简单的[API接口](http://doc.topvdn.com/api/index.html#!public-doc/SDK-Android/android_api.md)，实现直播推流、直播播放、云端录像播放、消息透传、视频通话等功能。

##2. 功能概要
![Alt text](./../images/usercase-android.png "羚羊云AndroidSDK功能")

- **直播推流**：将Android系统设备采集到的音视频数据进行编码，通过羚羊云自主研发的QSTP网络协议推送到羚羊云，以供终端用户观看直播或云端存储录像。支持自主设置分辨率、码率、帧率、编码类型等视频参数。

- **播放直播流**：支持播放直播流，网络拉流采用羚羊云自主研发的基于UDP的QSUP协议和基于TCP的QSTP协议，能够达到快速开流、低延时、高清画质的播放效果。

- **播放录像流**：支持播放云端录像流，网络拉流采用羚羊云自主研发的基于TCP的QSTP协议，能够达到快速开流、低延时、高清画质的播放效果。

- **视频通话**：客户端之间通过羚羊云自主研发的QSUP协议建立连接，相互发送接收数据进行视频通话。

- **消息透传**：提供客户端与客户端之间、客户端与服务端之间进行自定义消息格式通讯的能力。

##3. 功能特性
| ID | 功能特性 |
|----|----|
| 1  | 支持 H.264 和 AAC 软编（推荐） |
| 2  | 支持 H.264 和 AAC 硬编 |
| 3  | 软编支持 Android Min API 15（Android 4.0.3）及其以上版本 |
| 4  | 硬编支持 Android Min API 18（Android 4.3）及其以上版本 |
| 5  | 支持羚羊云自定义网络协议QSUP进行推流 |
| 6  | 支持羚羊云自定义网络协议QSTP进行推流 |
| 7  | 支持自主设置分辨率、码率、帧率 |
| 8  | 支持手机前置摄像头和后置摄像头切换 |
| 9  | 支持Zoom 操作 |
| 10 | 支持Mute/Unmute |
| 11 | 支持QSTP 推流自适应网络质量动态切换码率或自定义策略 |
| 12 | 支持纯音频推流，以及后台运行 |
| 13 | 支持自动对焦 |
| 14 | 支持闪光灯操作 |
| 15 | Android Min API 15 |
| 16 | 支持羚羊云自定义网络协议QSUP进行播放 |
| 17 | 支持羚羊云自定义网络协议QSTP进行播放 |
| 18 | 提供播放器核心类 IPlayer |
| 19 | 提供 LYPlayer 控件 |
| 20 | 支持画面旋转（0 度，90 度，180 度，270 度） |
| 21 | 支持 MediaCodec 硬件解码 |
| 22 | 可高度定制化的 MediaController |