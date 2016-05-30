#羚羊云C API手册
##1 数据类型
###1.1 返回值
```
#define LY_SUCCESS 0//操作成功
#define LY_FAILED -1//操作失败
```

###1.2 服务器信息
```
typedef struct ServerInfo
{
	unsignedcharserver_ip[32];//服务器IP，点分十进制格式字符串
	unsignedintserver_port;//服务器端口
	unsignedcharextradata[256];//服务器拓展信息
}ServerInfo_t;
```

###1.3 多媒体帧信息
```
typedef struct
{
    int                 frameType;   /*多媒体帧类型*/
    char*               frameBuffer; /*多媒体帧缓冲区地址*/
    unsigned long       frameLength; /*多媒体帧长度*/
    unsigned long long  frameTime;   /*多媒体帧时间戳*/
}MediaFrame_t;
```

###1.4 多媒体帧类型
```
/*多媒体帧类型*/
typedef enum {
	NALU_TYPE_SLICE = 1,//H264 P帧
	NALU_TYPE_DPA = 2,
	NALU_TYPE_DPB = 3,
	NALU_TYPE_DPC = 4,
	NALU_TYPE_IDR = 5,//H264 I帧
	NALU_TYPE_SEI = 6,//H264 SEI
	NALU_TYPE_SPS = 7,//H264 SPS
	NALU_TYPE_PPS = 8,//H264 PPS帧
	NALU_TYPE_AUD = 9,
	NALU_TYPE_EOSEQ = 10,
	NALU_TYPE_EOSTREAM = 11,
	NALU_TYPE_FILL = 12,
	NALU_TYPE_PREFIX = 14,
	NALU_TYPE_SUB_SPS = 15,
	NALU_TYPE_SLC_EXT = 20,
	NALU_TYPE_VDRD = 24,
	OPUS_TYPE_SAMPLE = 127,//OPUS音频帧
	AAC_TYPE_INFO = 128,//AAC音频配置信息
	AAC_TYPE_SAMPLE = 129//AAC音频帧数据
} FrameSampleType_Em;
```

###1.5 客户端状态枚举类型
```
typedef enum {
	CLIENT_STATUS_CODE_READY              =     1,      //初始状态
	CLIENT_STATUS_CODE_REQUEST_RELAY      =     2,      //正在请求转发
	CLIENT_STATUS_CODE_CONNECTING         =     3,      //正在连接中
	CLIENT_STATUS_CODE_WORKING            =     4,      //正在直播
	CLIENT_STATUS_CODE_STOPING            =     5,      //停止过程中
	CLIENT_STATUS_CODE_ERROR              =     255     //异常状态
} ClientStatus_Em;
```

##2 云服务接口
    
###2.1 启动云服务
```
int	LY_startCloudService(const char* const apToken, const char* const apConfig, const PlatformMessageCallBack apMessageCallBack, void *apUserData);
```
| - | - |
|-------|----|
| 接口名 | LY_startCloudService |
| 功能 | 启动云服务。调用了此api之后,平台相关凭证及资源开始准备，并且调用传入的回调函数apMessageCallBack通知云服务是否启动成功，其他接口必须在云服务启动成功之后才能正常使用。 |
| 返回值 | 0表示成功，非0表示失败。 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|apToken|char*|in|必须|用户token，由第三方后台生成。|
|apConfig|char*|in|必须|配置串，从第三方后台获取。|
|apMessageCallBack|PlatformMessageCallBack|in|必须|平台消息回调函数，该函数用来处理云平台返回的消息|
|apUserData|void*|in|可选|由SDK保存，平台每次调用aPMessageCallBack这个回调函数作为第一个参数传递进去，可传递一些用户自定义信息|
>**注意**：
>
**apToken**：用户token，由应用后台生成，格式如下：<br>
2147549953_1458979882_1469999882_bad3686a62a7aba595df3fb4c9c400e9。<br>
token的内容格式及意义请见<u>《羚羊云token认证机制》</u>(给出导航链接)
>
**apConfig**：配置串，从后台获取(无需解析)，格式如下：
[Config]\r\nIsDebug=0\r\nLocalBasePort=8200\r\nIsCaptureDev=1\r\nIsPlayDev=1\r\nUdpSendInterval=2\r\nConnectTimeout=10000\r\nTransferTimeout=10000\r\n[Tracker]\r\nCount=3\r\nIP1=121.42.156.148\r\nPort1=80\r\nIP2=182.254.149.39\r\nPort2=80\r\nIP3=203.195.157.248\r\nPort3=80\r\n[LogServer]\r\nCount=1\r\nIP1=120.26.74.53\r\nPort1=80\r\n<br>
调用者不必知道该字符串内容所表示的意义。
>
平台消息回调函数:
```
typedef void (*PlatformMessageCallBack)(void* apUserData, const char* constaMessage);
```
|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|apUserData|void*|out|必须|用户自定义数据，对于羚羊云透明，羚羊云只做保存和传递。|
|aMessage|char*|in|必须|羚羊云的回调数据，cJSON格式，需要解析，解析示例请参照羚羊云C接口DEMO。|

###2.2 停止云服务
```
void LY_stopCloudService();
```
| - | - |
|-------|----|
| 接口名 | LY_stopCloudService |
| 功能 | 调用了此api之后，相应的底层资源完全释放，建议在应用退出的时候调用，节省系统资源； |
| 返回值 | 无 |
| 参数列表 | 无 |

##3 推拉流接口
    
###3.1 建立传输通道
```
int LY_connect (const char *aUrl, const char *aDataSourceInfo)
```
| - | - |
|-------|----|
| 接口名 | LY_connect |
| 功能 | 和交互端（包括手机APP，羚羊云的服务器）建立连接，根据传入的aUrl参数判断类型，建立媒体传输通道。如果是观看录像使用此接口连接服务器，则第二个参数需要传录像磁盘信息；否则传入第二个参数传NULL即可。该函数与 disconnect配套使用。 |
| 返回值 | 大于等于0表示成功，且返回传输通道句柄fd；否则失败 |
| 参数列表 | 无 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aUrl|char*|in|必须|目标服务器的url地址，包含cid，连接类型（推流或拉流），使用的协议等。|
|aDataSourceInfo|char*|in|可选|如果是观看录像时调用，则此参数为必须。此参数内容从羚羊云后台或者第三方厂商后台获取，对调用者透明。|
>**注意**：
>
**aUrl**：连接地址，从后台获取到IP、端口和token，按照如下格式组合：<br>
topvdn://183.57.151.161:1935?protocolType=2&connectType=1&mode=2&token=1003469_3222536192_1493481600_5574318032e39b62063d98e6bff50069&cid=1003469<br>
topvdn://ip=%s:port=%d?protocolType=%d&connectType=%d&mode=%u&token=%s&cid=%lu&begin=%lu&end=%lu&play=%lu<br>
Url各字段意义及详解请见《羚羊云Url格式解析》

###3.2 断开通道连接
```
int	LY_disconnect(const int aFd);
```
| - | - |
|-------|----|
| 接口名 | LY_disconnect |
| 功能 | 根据传入的fd参数，断开对应传输通道的连接，与 LY_connectToServer或LY_connectToClient配套使用。 |
| 返回值 | 0表示成功，非0表示失败 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aFd|int|in|必须|传输通道句柄|

###3.3 发送媒体帧
```
int	LY_sendMediaFrame(const int aFd, MediaFrame_t * apMediaFrame);
```
| - | - |
|-------|----|
| 接口名 | LY_sendMediaFrame |
| 功能 | 以数据帧为单位向已连接成功的传输通道发送音视频数据。 |
| 返回值 | 0表示成功，非0表示失败|
| 参数列表 | 无 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aFd|int|in|必须|传输通道句柄|
|apMediaFrame|MediaFrame_t|in|必须|多媒体数据帧结构体指针，详细参考数据类型中的媒体帧信息|

###3.4 接收媒体帧
```
int	LY_recvMediaFrame(const int aFd, MediaFrame_t * apMediaFrame);
```
| - | - |
|-------|----|
| 接口名 | LY_recvMediaFrame |
| 功能 | 从传输通道接收媒体数据。该接口在没有数据到来时会阻塞，且需要外面管理接收数据的内存空间。 |
| 返回值 | 0表示成功，非0表示失败|
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aFd|int|in|必须|传输通道句柄|
|apMediaFrame|MediaFrame_t|in|必须|多媒体数据帧结构体指针，详细参考数据类型中的媒体帧信息|

###3.5 获取在线状态
```
int	LY_getOnlineStatus(void);
```
| - | - |
|-------|----|
| 接口名 | LY_getOnlineStatus |
| 功能 | 获取本方在云平台的在线状态（由于网络传输存在延迟，可能出现返回状态与实际状态不同步的现象。） |
| 返回值 | 0表示离线，1表示在线 |

###3.6 更新token信息
```
int LY_updateToken(const char *aDeviceToken, int maxLen);
```
| - | - |
|-------|----|
| 接口名 | LY_updateToken |
| 功能 | token过期后可调用此接口更新token信息。|
| 返回值 | 0表示成功，非0表示失败 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aDeviceToken|char*|Out|必须|接收token信息的内存空间的指针。|
|maxLen|int|In|必须|接受token信息内存空间的最大长度。|
>**注意**：
>
**aDeviceToken**：用户token，由应用后台生成，格式如下：<br>
2147549953_1458979882_1469999882_bad3686a62a7aba595df3fb4c9c400e9。<br>
token的内容格式及意义请见<u>《羚羊云token认证机制》</u>(给出导航链接)

###3.7 跳转到指定时间点录像
```
int LY_seek(const int aFd, const unsigned int aCurrentTime);
```
| - | - |
|-------|----|
| 接口名 | LY_seek |
| 功能 | 跳转到指定录像时间点，指定调转的时间点范围在打开该录像通道的开始时间和结束时间内。时间精度为毫秒。 |
| 返回值 | 0表示成功，非0表示失败 |
> 

|参数列表|类型|In/Out|可选/必须|描述|
|-------|----|----|----|----|
|aFd|int|in|必须|建立录像传输通道时的通道句柄fd|
|aCurrentTime|unsigned int|in|必须|要跳转的时间点，该时间点为相对于建立录像传输通道时传入的begin的差值，必须大于0 |