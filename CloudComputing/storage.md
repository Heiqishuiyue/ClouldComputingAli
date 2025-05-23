# 云存储
<img src="./Iamge/OSSvsCPFSvsNAS.png" width="800" />

## 云存储优势
* 超大规模
* 高可扩展性
* 高可靠性和高可用性
* 按需服务
* 透明服务
* 管理和维护


> 公共云计算中使用的存储并非共享存储，而是分布式存储。

## 文件存储系统
### 存储类型
* 通用型NAS
* 极速型NAS - 时延敏感型企业引用
* 文件存储CPFS - IO密集型
    * 文件存储CPFS支持三种存储规格，分别为100 MB/s/TiB基线、200 MB/s/TiB基线和400 MB/s/TiB基线（CPFS智算版）。
    * CPFS特有的**数据流动**功能可以实现将对象存储OSS中的数据合并入CPFS，进行统一命名空间的元数据管理，快速处理OSS中的数据。

> 不同存储类型的文件系统不能相互转换

### 文件存储NAS的访问协议
* NFS协议：适合Linux
* SMB协议：适合Windows

> 文件系统与协议类型强绑定，创建后无法改变。

> 通用型NAS支持NFS v3和NFS v4.0， 极速型NAS仅支持NFS v3。**NFS v3的性能是优于NFS v4.0**。

> 如果需要多个计算节点同时修改同一个文件，则需要NFS v4.0挂载以支持文件锁功能。

### 文件存储HDFS版
* 允许我们像Hadoop分布式文件系统中一样管理和访问数据。
* 多个计算节点可以同时访问同一个文件存储HDFS版实例，共享问价和目录。
* 在HDFS中，权限组是白名单。
* 初始下，每个阿里云会自动生成一个默认权限组，默认权限组不支持删除或修改。
* 不支持快照，不支持ACL。

### 多台ECS实例批量挂载同一NAS文件系统
* 仅支持Linux。
* 仅支持状态为**运行中**的ECS实例。
* ECS实例必须和NAS文件系统在同一地域内。
* ECS实例和NAS可以在同一地区的不同可用区，所在VPC已打通即可。
### 备份
NAS控制台可以定期备份NAS文件，备份文件会存储到**混合云备份HBR**，现以改名为**云备份**。
### 回收站
开启回收站后，以实现文件误删除后恢复操作。
# 存储块
## 本地盘
* 来自单台物理机
* 没有三副本机制
* 不支持：挂载本地盘，**使用快照创建本地盘**，**为本地盘创建快照**

## 专属块存储集群
* 支持作为ECS实例的系统盘。
* 支持作为ECS实例的数据盘
* 分为基础性专属块存储集群和性能型专属块存储集群。
* 不支持变配操作。
* 仅支持包年包月的计费方式。

## 云盘
* 三副本
    * 有三类角色，Master、Chunk Server和Client。
    * 三副本分布在不同Chunk Server上。
    * 数据节点损坏，导致有效副本数量小于三是，集群会发起自动同步任务，确保有效副本达到三份。
    * 不能解决人为误删导致的数据丢失。
* 可以定期创建快照
* 数据擦除机制

分类
|  类型   | 访问延时  | 选型和说明 |
|  ----  | ----  |  ----   |
| ESSD云盘| 小于等于0.5ms | 大型OLTP数据库（MySQL、 postgresql、 Oracel、 SQL server）、NoSQL数据库、ElasticSearch
|SSD云盘 | 0.5-2ms |适合I/O密集型、中小型数据库
|高效云盘 | 1-3ms | 开发和测试环境
|普通云盘 | 5-10ms| 不推荐



### 云盘加密
* 创建ECS实例后为云盘勾选加密后自动加密，无需自建和维护密钥管理基础设施。
* 利用密钥管理服务KMS加密。
* 使用加密后，系统会将从ECS实例传输到云盘的数据自动加密，并在读取时自动解密。
* 启用加密动能的云盘性能会低于未开启加密功能的云盘。

### 创建云盘
* 创建Linux的ECS，增加数据盘，需格式化数据盘，然后挂载数据盘。/dev/xvda/为系统盘。
* 创建Windows的ECS，不需要任何操作。

### 挂载数据盘
* 实例与云盘在同一可用区。
* 被挂载实例为运行中或已停止，不能为已锁定。
* 云盘的状态为待挂载（Available）。
* 一台ECS最多挂载64块数据盘。

### 更换系统盘
* 系统盘ID会变。
* 系统盘的**云盘类型**、**实例IP地址**以及**弹性网卡MAC地址**保持不变。
* 自动快照和数据会丢失。
* 手动创建的快照不会丢失，不会释放。
* 如果需要保留系统盘数据，减少业务中断时长，建议使用服务器迁移中心SMC迁移操作系统。
* 更换到Linux，会无法识别NTFS格式，需在下载ntfs-3G.
* 更换到Windows，会无法识别ext3，ext4，xfs等格式，需安装下载ext2read，ext2fsd等。
### 扩容系统盘
在更换系统盘的基础上指定更大的系统盘容量。不会扩容系统文件。

### 初始化系统盘
* 已创建快照。初始化会清空云盘写入的数据。
* 重新初始化必须先停止ECS实例。
* 安装的应用程序和产生的数据都会被删除。
* 自动快照，重新初始化后，设置依然有效。
* 不会改变ECS实例的IP地址和云盘ID。
* 不会删除系统盘的快照。

# 对象存储OSS
海量、安全、低成本、高可靠的云存储服务。可提供99.9999999999%数据持久性， 可用性99.995%。

## 原理&概念
* 数据以对象Object的形式存储在存储空间Bucket中。创建Bucket需指定地域、访问权限、存储类型等属性。指定Object的文件名key作为其唯一标识。
* 距离近的地域访问速度最快。
* 以HTTP Restful API的形式对外提供服务，访问不同地域要不同的访问域名。
* Object操作具有**原子性**，操作要么成功要么失败。
* Object操作具有**强一致性**。一点收到上传成功的相应，上传的Object就已经可以读，且数据已经冗余写入多个设备中。
* 支持多种数据处理能力，如图片处理、视频截帧、文档预览、图片场景识别、SQL查询等。
* 没有大数据处理能力。

## 数据冗余机制
* 基于**纠删码**、多副本的数据冗余机制。
* 可支持两个存储设施并发损坏时，仍维持数据不丢失。
* 支持机房级别的容灾能力。

## 管理方式
* 通过控制台管理。
* 通过API或者SDK管理OSS。
* 通过工具管理：图形化管理工具ossbrowser、命令行工具 ossutil、FTP 管理工具、Bucket 挂载工具ossfs。
* 通过存储网关管理OSS。实现使用本地文件夹的方式使用OSS存储。
* 可以通过一下工具进行下载文件夹：SDK、ossutil、ossbrowser、 REST API。

## 存储类型
| 对比指标 | 标准存储 | 低频存储 | 归档 | 冷归档 
|  ----  | ---- | ---- | ---- | ---- |
| 数据取回费用 | 无 | 按实际获取的数据量收取，单位GB | 按实际解冻量| 按实际解冻是选择的数据取回能力以及数据大小|
|图片处理|支持|支持|支持但需解冻|支持但需解冻|
|数据访问特点|实时访问毫秒级|实时访问毫秒级|需解冻，需一分钟|需解冻几个小时，按选择的取回能力决定|
|适用场景|各种社交、分享图片、音视频应用、大型网站、数据分析、程序下载|较低访问频率，平均每月访问1到2次的业务场景|数据长期保存的业务场景，如档案、医疗影像|需要超长时间存放的极冷数据，如大数据及人工智能领域长期积累的原始数据|

* 标准存储：本地冗余LRS和同城冗余LRS。
* 低频访问存储：本地冗余LRS和同城冗余LRS。
* 同城冗余数据设计持久性99.9999999999（12个9），本地冗余则为11个9。
* 服务可用性同城比本地高。

## 存储空间Bucket
在是上传数据到OSS之前，您需要在OSS所支持的地域中穿件一个存储空间，然后将无限量的对象Object上传到Bucket中。

存储空间是用户用于存储对象（Object）的容器，所有的对象都必须隶属于某个存储空间。存储空间具有各种配置属性，包括**地域、访问权限、存储类型**等。用户可以根据实际需求，创建不同类型的存储空间来存储不同的数据。
### 命名规范
* 小写字母、数字、-
* 长度3-63

### 特点
* 与ECS地域相同，地域不可修改。
* 内部是扁平，没有文件体统目录等概念。
* 名字是全局唯一，创建后无法修改。
* 没有空间的限制，自动扩容。

### 使用限制：
* 同一地域内创建的存储空间总数不超过100个。
* 名称在OSS范围内必须唯一。
* 创建存储空间后，其名称、所在地域、存储类型、冗余类型不支持修改。
* 单个存储空间的容量不限制。

### 通过云存储网关挂载OSS
通过云存储网关将OSS的Bucket作为文件系统挂载至ECS中使用。

* 文件网关将OSS Bucket对象结构与NAS文件系统的目录和文件建立映射关系。
* 块网关在OSS中创建**存储卷**，提供Internet小型计算机系统接口（iSCSI）协议访问。 

#### 使用限制
* 已开通云存储网关。
* 已创建与目标Bucket同地域的专用网络VPC和交换机。
* 已创建与OSS同地域的ECS实例。


# 对象Object
Object是OSS存储数据的基本单元，也被称为OSS的文件。
## 对象构成
* key: 对象ID
* Data: 对象数据
* Meta: 对象属性
## 对象特征
* **不支持修改**，可以覆盖。
* 对象没有目录层级结构的关系，文件名称可以有/，看起来有目录。
* 命名不能已正斜杠或反斜杠开头。
* 单个文件最大**48.8TB**。

## 上传和管理Object
|上传方式|API|适用场景|
|-|-|-|
|简单上传|PutObject|小文件不超过5G，可以携带Object Meta信息。|
|追加上传|AppendObject|小文件不超过5G。生成的Appendable类型的Object可以直接追加内容。|
|表单上传|PostObject|不超过5G。HTML页面中上传Object。|
|分片上传|Multipart Upload|大文件，最大不超过48.8TB。可进行碎片化管理。网络条件较差的时候。|
|断点续传|-|适合大文件，支持并发、断点续传、自定义分片大小。最大不超过48.8TB|

* GetObject：下载Object。
* CopyObject：拷贝Object。
    * 同个Bucket或同地域的Bucket之间拷贝文件。不需要再次上传至OSS，可以节省OSS网络带宽。
    * Put请求Header中添加元素x-oss-copy-source来指定Object，即可实现设置Object头。
* DeleteObject：删除单个Object。

### 简单上传
* 适合小文件，不超过5G。
* 有同名则覆盖。
* 基于put或post方式的http请求。
* 简单上传支持**使用OSS控制台、图形化管理工具ossbrowser、使用阿里云SDK、命令行工具 ossutil、使用REST API**。


**Browser.js** SDK支持以下几种文件上传方式：

* 当您需要将File对象、Blob数据以及OSS Buffer上传到OSS时，您可以使用简单上传的方式。具体操作，请参见简单上传。

* 当您需要上传大于100 MB且小于48.8 TB的文件时，您可以使用分片上传的方式。分片上传是指将要上传的大文件分成多个分片（Part）来分别上传，上传完成后再调用CompleteMultipartUpload接口将这些Part组合成一个文件从而实现断点续传的效果。

* 当您希望在文件上传过程中因网络异常或程序崩溃导致文件上传失败时，可继续上传未完成的部分，您可以使用断点续传上传。

* 当您希望文件上传完成时提供回调（Callback）给应用服务器上传，您还可以使用上传回调。

**OSSutil**命令：
* **-c，--config-file**	ossutil工具的配置文件路径，ossutil启动时将从配置文件读取配置。当您需要管理多个账号下的Bucket时，可以生成多个配置文件，并将其中一个指定为默认配置文件。当您需要管理其他账户下的Bucket时，请通过-c指定正确的配置文件。
* **-e，--endpoint** 指定Bucket对应的Endpoint，当您需要管理多个地域的Bucket时，可以通过此选项指定多个Endpoint。
* **-k，--access-key-secret** 指定访问OSS使用的AccessKey Secret，当您需要管理多个账号下的Bucket时，可通过此选项指定对应的AccessKey Secret。
* **--acl**	配置读写权限ACL。
### 追加上传
* 适合小文件，不超过5G。
* 生成的Object类型为Appendable，且仅支持在Appendable类型的Object后**直接追加内容**，且每次追加的上传数据都能够及时可读。

### 表单上传
* 适合小文件，不超过5G。
* 表单上传适用于在HTML网页上上传Object。
### 分片上传
* 上传大文件，可以使用分片上传，最大不超过48.8TB。
* 需支持断点上传。
* 网络条件较差，和OSS的服务器之间的链接经常断开。
### 断点上传
* 通过断点续传的方式将文件上传到OSS前，您可以通过Checkpoint文件指定断点记录。如果**上传中出现网络异常或者程序奔溃导致文件上传失败时，将从断点记录处继续上传为上传完成的部分**。
* 适合大文件，支持并发、断点续传、自定义分片大小。
* 最大不超过48.8TB。
* 当前仅支持通过SDK的方式实现断点续传上传。

## 复制Object
* 可以使用OSS OpenAPI的CopyObject来实现。使用CopyObject可以节省API请求的次数。只调用一次CopyObject接口只对目标Object所在的Bucket增加一次put请求次数。

## 标签管理
* 存储空间的标签功能对Bucket进行分类管理。
* **对象标签**对存储空间的中的Object进行分类。

## Object删除方式
* 生命周期规则： 可以基于**最后一次修改时间以及最后一次访问时间**的策略创建生命周期规则，定期将多个文件存储转为指定存储类型，或者将过期的Object和碎片删除，从而节省存储费用。
* 如您需要查看OSS文件被访问、删除等操作的记录，需要开启OSS的Logging日志。如果要定位删除的操作记录，需要在访问日志中过滤DELETE关键字来定位。

# WEB直传
## 服务费签名直传
服务端生成签名以实现Web端直传，使用户能够通过Web浏览器直接使用PostObject接口上传文件到OSS。此过程通过签名机制确保上传的安全性。同时，根据具体业务需求，您可以在服务端生成上传策略（Policy），以限制上传操作。

配置OSS：在OSS控制台创建一个Bucket，用于存储用户上传的文件。同时，为 Bucket 配置跨域资源共享（CORS） 规则，以允许来自服务端的跨域请求。
## 客户端签名支直传
客户端签名直传是指从服务端获取sts token，并使用sts token生成签名，最后使用此签名上传文件到OSS。客户端可以直接将文件上传至OSS，减少了中间环节，因此可以加快上传速度。本文介绍如何进行客户端签名直传。


# 图片处理服务IMG
* 支持海量数据处理，可用性高。
* 需要收费，包括图片处理、请求费用、流量费用。
## 常见功能
* 获取图片信息
* 图片格式转换
* 图片缩放、裁剪、旋转
* 图片添加图片、文字、图文混合水印。不支持图片文字识别。
* 自定义图片处理样式。
* 通过管道顺序调用多种图片处理功能。

## 处理图片的方式
* 通过文件URL处理图片：通过URL处理图片的方式有两种，一种是添加图片处理参数，另一种是添加图片样式参数。多个参数通过正斜线（/）连接。
* 通过SDK处理图片：在SDK中添加图片参数或图片样式参数的方式来处理图片。
* 使用REST API：通过在GetObject接口中添加图片处理参数或图片样式参数的方式处理图片。

## 图片处理的限制
* 处理图片是OSS用户上传到Bucket里的Object。
* 图片格式只支持JPG、PNG、BMP、GIF、WebP、TIFF。
* 原图大小不能超过**20MB**。
* 除旋转对应的原图高或者宽不能超过**4096px**，其他操作对应原图高或者宽不能超过30000px。且总像素不能超过2.5亿px。
* 缩放后图片限制宽或者高不能超过16384px。
* 仅支持对动态图片（例如GIF格式图片）进行缩放、裁剪、旋转以及添加图片水印的操作。
* 每个存储空间下最多创建50个样式。
## 图片处理
* 对象存储OSS图片处理服务默认不保存处理后的图片。
* 可以通过URL或者SDK的方式进行图片处理持久化。
* 原图所在Bucket和处理后图片转存的目标Bucket可以相同也可以不同，但必须属于同一账号下的相同地域。
## 管道Channel
* Channel是IMG上的命名空间，也是计费、权限控制、日志记录等高级功能的管理实体。
* IMG名称在整个图片处理服务器中具有全局唯一性，且不能修改。
* 每个用户最多可创建10个Channel，每个Channel放Object数量没有限制。
* Channel跟OSS的Bucket相对应，即用户只能创建与自己在OSS上Bucket同名的Channel。

# 数据安全
## 访问控制
### 授权
* 基于用户授权RAM/STS Policy
* 基于资源授权Bucket Policy

### 读写权限ACL
* Obejct优先于Bucket。
* 共有读写 public-read-write。
* 公共读，私有写 public-read。
* 私有读写 private。
## 权限控制
* 开发环境和正式环境中的应用使用不同的Bucket。
* 允许某个应用读，另一个应用写通一个Bucket里的文件。
* 让某个应用临时读写某个Bucket，凭证过期后无法访问。
* 不同的应用是应该访问不同的Bucket，权限不仅需要有允许和拒绝，也需要区分读和写。
* 使用STS临时访问凭证访问OSS。
* 鉴权失败返回403.

## 数据加密
服务端加密：

* 使用KMS托管密钥加密。
* 使用OSS完全托管加密。

客户端加密：

* 使用KMS托管加密用户主密钥。
* 使用用户自助管理密钥。

# 版本控制
* 版本控制是针对Bucket级别的数据保护功能，数据的覆盖和删除都会将以历史版本的形式保留下来。
* 新建Bucket时指定，之后不能再变更。
* 版本控制有三种状态： 未开启、开启、暂停。一旦开启，无法返回至未开启。
* 同一Bucket中，版本控制与合规保留策略无法同时配置。
* 如果Bucket开启了版本控制，上传文件时附加的覆盖同名文件请求头x-oss-forbid-overwrite将不在生效。

# 合规保留策略
* Bucket级别。
* 允许一条。
* 基于**时间**的合规保留策略。

# 防盗链
OSS支持对Bucket设置防盗链，即通过对访问来源设置白名单机制，避免资源被其他人盗用。

原理实现：
设置HTTP请求头Referer白名单以及是否允许空Referer，限制仅白名单中的域名可以访问您的Bucket内的资源。

校验规则：
仅当通过签名URL或者匿名访问Object时，进行防盗链验证。请求的Header中包含Authorization字段，不进行防盗链验证。

## 防盗链匹配原则
* 如果Referer白名单为空，则允许所有请求。
* 如果白名单不为空，且不允许空Referer，则只有Referer属于白名单的请求被允许，其他请求，包括Referer为空的请求都会被拒绝。
* 如果白名单不为空，且允许空Referer，则Referer为空的请求和符合白名单的请求被允许，其他请求，包括Referer为空的请求都会被拒绝。

## 匹配方式
> 支持通配符匹配，通配符*表示使用星号代替0个或者多个字符，通配符问号？代替一个字符。

# 数据容灾
* 同城冗余存储。可以满足关键业务系统对于恢复时间目RTO以及恢复点目标RPO等于0的强需求。
* 跨区域复制。跨不同OSS数据中心（地域）的Bucket自动、异步（近实时）复制对象，它将Object的创建、更新和删除操作从源存储空间复制到不同区域的目标存储空间。
# OSS高防
OSS高防是OSS结合DDoS高防推出的DDoS攻击代理防护服务。当收保护的存储空间遭受大流量攻击时，OSS高防会将攻击流量牵引至高防集群进行清洗，并将正常访问流量回源到目标Bucket，确保业务能够正常进行。
## 防护原理
OSS默认使用DDoS原生防护。当用户开通了OSS高防后，**当攻击频率超出了DDoS原生防护的防御阈值后**，OSS高防会将攻击流量牵引至高防集群进行清洗，并将正常访问流量回源到目标Bucket，确保业务能够正常进行。攻击结束后，受到攻击Bucket会切回至DDoS原生防护。

## 使用限制
* 每个地域可以创建一个高防实例，每个实例只能绑定同一地域下的10个Bucket。
* 在Bucket绑定高防实例后，用户无法通过浏览器预览Bucket内的资源。OSS默认不对Bucket关联的自定义域名进行防护。如需防护，要在OSS高防控制台添加要防护的自定义域名。每个Bucket防护的自定义域名数量上限是5个。

# 回源
当请求者请求对象数据OSS不存在时，本应该返回404。如果设置了回源规则，请求者即可通过回源规则从OSS获取正确的数据。

> 回源分为**镜像回源**和**重定向**。镜像回源主要用于数据无缝迁移。

# 访问OSS
* 通过外网访问OSS。外网指的是互联网。通过外网访问的写入流量（写）是免费的，流出流量（读）是收费的。方式：1. 以URL的形式访问OSS Object；2. 通过OSS SDK 配置外网访问域名。

* 通过内网访问OSS。内网产生的流入流出流量均免费，但是请求次数仍会计费。方式：1. 以URL的形式访问OSS Object；2. 通过ECS使用OSS SDK配置内网Endpoint。

* 同地域内网互通，跨地域内网不互通。
* 外网地址：https//{BucketName}.oss-cn-hangzhou.aliyuncs.com
* 内网地址：https//{BucketName}.oss-cn-hangzhou-internal.aliyuncs.com

* 与不同地域的ECS实例或者公网用户可以通过配置ECS反向代理，间接实现通过OSS内网地址访问OSS资源。
* 通过在ECS实力上配置OSS的反向代理，实现通过固定IP地址访问OSS的存储空间。
# 自定义域名
* 已注册且绑定中国内地的Bucket的域名已在中国工信部备案。
* 自定义域名支持传输加速功能。
* 通过托管证书，让自定义域名支持HTTPs。
* 在OSS管理控制台中可以选择自动添加或者修改域名的CNAME记录。（可以选择“自动”或者“手动”添加域名解析）。
* OSS域名绑定仅支持**三级域名**。
* 绑定自定义域名，可以在浏览器直接打开这类文件，而不是直接下载。
# 通过Web端上传
* 表单上传
* OSS Browser.js SDK
* 支付宝、微信小程序
# 静态网站托管
* 如果网页都由静态内容构成，可以使用静态网站托管将网站托管到OSS的存储空间，使用Bucket的访问域名或者自定义域名。
* 静态网站是指网页都是由静态内容构成，包括客户端执行的脚本，例如JavaScript。
* 如果托管的是HTTPS，静态网页，需要托管SSL证书。
* 默认首页是用户通过浏览器访问静态网站域名时，OSS返回的网站首页。
* 默认404页是指Bucket内文件出现404错误时，OSS返回的页面。
* 默认首页和默认404页指定文件的读写权限ACL设置为public-read，确保改文件允许匿名访问。
* 如需确保访问静态网页是预览行为，您必须为Bucket绑定自定义域名。
# 访问日志转存
* OSS会将这个Bucket的访问日志，以小时为单位，按照固定命名规则，一般按日期命名，生产一个Object存放到指定的Bucket中。
* 生产日志的源Bucket和存储日志的目标Bucket可以相同也可以不相同，但必须属于同一个账号下的相同地域。
* 关闭日志转存前，OSS的日志会一直生成。用户可以通过生命周期规则，基于最后一次修改时间，定期删除日志。

# 数据迁移
> **ossimport**是一款讲数据迁移至OSS的工具。本身不收费。
# 传输加速
OSS传输加速利用全球分布的云机房，将全球各地用户对您Bucket的访问，经过智能路由器解析至就近的接入点，使用优化过后的网络协议，为云存储的上传、下载提供端到端的加速方案。
* 开启传输加速后必须用OSS的传输加速域名才会提升访问速度。
* 传输加速Endpoint仅支持HTTP/HTTPS协议的API接入，不支持RTMP协议的非HTTP/HTTPS协议的API接入。
* 开启全球加速后，Bucket会保留默认Endpoint的基础上，新增一下两种传输加速Endpoint。
* 全球加速Endpoint：oss-accelerate.aliyuncs.com。
* 非中国内地加速Endpoint：oss-accelerate-overseas.aliyuncs.com。
# 计费
* OSS API请求费用付费方式只支持按量付费。
* 存储费用的付费方式支持包年包月、存储容量单位包SCU、按量付费。
* 上传：内网，外网都不收费。
* 下载：内网不收取费用，外网收取流量费用。
* 计费为小时为周期。
* 资源包最终呈现出来的效果还是按量计费。
* 资源包抵扣顺序为，地域存储包>中国大陆通用存储包>SCU>按量计费。
* **请求者付费模式**：请求者支持访问数据时产生的费用，而Bucket拥有者支付存储费用。
* 除去基础的存储、流量、请求费用外，增值服务也需要付费：数据处理费用、对象标签费用、传输加速费用、临时存储费用、DDos防护费用、敏感数据保护费用、OSS违规检测费用。
* 降低费用的方案：内网访问、使用CDN、合理安排存储类型，定期删除过期数据、资源包。

# OSS违规检测
OSS违规检测能够检测阿里云对象存储OSS服务中的图片、视频是否包含色情、涉政等违规内容，并可以自动冻结检测出的违规内容（禁止通过公网访问这些违规内容）
* 使用OSS违规检测前，需要在内容安全控制台通过RAM授权内容安全服务读取OSS Bucket的权限。
* 增量扫描：支持自动检测。您可以为指定的OSS Bucket开启增量扫描，让内容安全自动扫描Bucket中新增的图片、视频。增量扫描一次配置即可长期生效。
* 存量扫描：不支持自动检测。如果需要对指定OSS Bucket中已有的图片、视频进行检测，您必须手动创建一个存量扫描任务，在任意时间，一次性扫描Bucket中已上传的图片、视频。
* 支持多种违规检测，场景包括：涉黄、涉政、图文违规、不良场景。
* 支持检测特定格式后缀的文件。
* 冻结的方式支持：
    1. 将违规文件访问权限设置为私有。
    2. 将违规文件进行删除。
    3. 将违规文件移动到Bucket中的其他目录下。
## 综合实践
* 基于 OSS 并使用**MaxCompute**构建 PB 级数据仓库。 通过 MaxCompute 对 OSS 上的海量数据进行分析,将您的大数据分析工作效率提升至分钟级,帮助您更高效、更低成本的挖掘海量数据价值。

# 云数据库RDS
## 产品定位
* 基于阿里云分布式文件系统和SSD盘高性能存储。
* RDS支持MySQL、SQL Server、PostgreSQL和MariaDB引擎。
* 具备容灾、备份、恢复、监控、迁移等方面全套解决方案。
## 规格族
云数据库RDS根据CPU、内存、连接数、IOPS，提供多种实例规格族：
* **共享规格**：追求高性价比，需要减轻使用成本的应用场景。高度复用CPU。
* **通用规格**：对性能稳定性要求较低的应用场景。
* **独享规格**：以数据库为和兴系统的业户场景，例如金融、电商、政务、大中型互联网业务等。

## 存储类型
提供了三种：**本地SSD盘、ESSD云盘、SSD云盘**。更新文档后还加了**通用云盘**。
* 本地SSD盘：所属的RDS实例都是一主一备（高可用版）架构，主节点故障时，主备节点秒级完成切换。
* SSD云盘或ESSD云盘：分布式云盘，通过多副本冗余确保数据可靠性。

## 只读实例
对数据库有少量写请求，但有大量读请求的情况见，为实现读取能力的弹性扩展，分担数据库压力，可以创建一个或者多个只读实例。
* 创建只读实例时会从备用实例复制数据，数据与主实例一致，主实例数据更新后立即自动同步到只读实例。可以在只读实例上设置延时复制。
* 只能在主实例内创建只读实例，不能将已有的实例切换为只读实例。
* 只读实例和主备实例在同一地域，可以在不同可用区；备用实例和主实例在同一地域；灾备实例和主实例在不同地域。
## RDS MySQL产品系列
| 系列 | 说明 | 适用场景 |
| ---- | ---- | ---- |
| 基础系列 | · 单节点，计算与存储分离。<br> · 不支持增加只读实例。 | · 个人网站。<br> · 微型网站。<br> · 中小企业的开发测试环境。 |
| 高可用系列| · 一主一备的高可用架构，支持自动故障切换。备节点不可访问。<br>· 支持增加只读实例扩展读能力。| · 大中型企业的生产数据库。<br>· 互联网、物联网、零售电商、物流、游戏等行业的数据库。 |
| 集群系列 | · 一主多备的高可用架构，支持自动故障切换。备节点可访问，提升读能力。<br> · 可增加更多备节点进一步扩展读能力。 | · 大中型企业的生产数据库。<br>· 互联网新零售行业、汽车制造行业、企业大型ERP系统等。|
| 三节点企业版 | · 采用**一主一备一日志**的三节点架构，通过多副本同步复制，确保数据的强一致性，提供金融级的可靠性<br>· 提供企业级可靠性，满足恢复点目标RPO（Recovery Point Object）=0 | · 对数据安全性要求非常高的金融、证券、保险行业的核心数据库。 |
## RDS MySQL分析实例
RDS MySQL提供了分析实例功能，可以将RDS MySQL主实例中的数据自动同步到MySQL分析实例中，解决RDS MySQL复杂分析卡顿问题，实现毫秒级正对亿万数据进行及时的多维分析透视和业务探索。
## RDS MySQL Serverless实例
RDS MySQL Serverless实例是正对中小型企业或者个人开发者推出的一款数据库。
* **提供了CPU、内存的实时弹性能力，构建云盘架构下的RDS MySQL产品的新形态**。
* 打破固定资源付费的模式，真正负载与资源动态匹配的按量付费，可节省大量成本。
* 对高吞吐写入场景和高并发业务进行了设计优化，同时提供了弹性伸缩能力，适合业务数据量大、并具有典型的业务访问波峰波谷场景。

## RDS MySQL 创建数据库和账号
RDS MySQL或者RDS MySQL Serverless实例支持两种数据库账号：高权限账号和普通账号。
| 账号类型 | 说明  |
| ---- | ---- |
|高权限账号|1. 只能通过控制台或者API创建。<br> 2.一个实例中只能创建一个高权限账号，可以管理所有普通账号和数据库。<br> 3. 拥有实例所有数据库权限，可以断开任意账号的连接。  |
|普通账号| 1. 可以通过控制台、API或者SQL语句创建和管理。<br> 2. 一个实例可以创建多个普通账号，具体数量与实例内核有关。|

* 账号类型无法切换。
* 如果高权限账号自身出现问题，例如被意外回收，可以通过重置账号权限的方式恢复。

### RDS MySQL表单大小限制
单表的有效最大表尺寸通常受限于操作系统的文件尺寸限制，而不是受MySQL内部机制的限制。
* 由于存在部分元数据的开销，RDS MySQL单表的最大尺寸为略小于2TB。
* 表中记录数在2000万条以内。
* 表的总大小在10GB以内。
## 变更实例配置
前提：
* 实例的计费方式为包年包月或按量付费。
* 实例为运行中。
* 没有未支付的续费账单。

注意事项：
* 变配操作无需手动重启实例。
* 变配操作不会导致实例ID和连接地址的改变。
* 升级主实例配置时，只读实例不会自动升级，需要自己手动升级。
* 实例变更支持升降配。
* 只读实例的存储空间必须大于或等于其所属主实例的存储空间。建议先扩容只读实例的存储空间，所有只读实例扩容完成后，再扩容主实例存储空间。
* 本地盘实例缩容后的空间必须大于或等于一用实例存储空间的120%。

## 数据库审计
数据库审计服务是一款专业、主动、实时监控数据库安全的审计产品，可用于审计阿里云平台中的**RDS云数据库、ECS自建数据库和NoSQL数据库**。为云上数据库提空安全诊断、维护、管理能力。
### SQL洞察和审计
SQL通查和审计功能在全量请求和安全审计的基础上，融合了搜索、SQL洞察、安全审计等功能，帮你用户更好地获取SQL语句的具体信息、性能问题排查、识别高风险来源，提供流量回放和压测功能，帮助用户验证实例规格时候需要扩容，有效应对业务流量高峰。
* 搜索功能：查询并导出SQL语句以及对于的各种信息，如数据库、状态、执行时间等。
* SQL通查功能：对SQL进行健康情况诊断、性能问题排查、业务流量分析。
* 安全审计功能：可自动识别高危SQL、SQL注入、新增访问来源等分险。
* 流量回放和压测功能：帮助用户验证实例规格时候需要扩容，有效应对业务流量高峰

## RDS备份
* RDS通过数据传输服务（DTS）实现主实例和异地灾备实例之间的实时同步。 
* 主实例和灾备实例均搭建主备高可用架构，当主实例所在区域发生突发性自然灾害等状况，主实例（Master）和备实例（Slave）均无法连接时，可将异地灾备实例切换为主实例，在应用端修改数据库连接地址后，即可快速恢复应用的业务访问。
### 分类
* 数据备份（全量备份）：恢复备份所在时间点的数据。
* 日志备份：增量备份，开启日志备份后，本地日志会上传，形成日志备份。
* 数据备份加上日志备份可以恢复时间范围内任意时间点的数据。
## 数据恢复
* 按备份集恢复：用户可以将指定备份及的数据恢复到一个临时实时或者克隆实例上。
* 按时间点恢复：用户可以选择临近时间点，系统根据全量备份以及之后的日志备份，将数据重新放到一个临时实例或克隆实例上。
### 恢复库表
RDS MySQL支持常规和极速级别的库表恢复功能，您可以将指定的库、表按备份集或时间点恢复至原实例或新实例，无需恢复全部数据，可用于误操作后的快速订正，以及分析历史数据等场景。

* 新实例默认开启库表恢复功能，该功能开启后无法关闭。
* 常规的库表恢复功能默认开启，不会产生费用。
* 恢复到原实例时，恢复后库名可与原库名相同，但恢复后表名不能与原表名相同
## 综合应用
### IOPS使用率高
您可以通过如下任意一种途径或结合使用两种途径来解决 IOPS 使用率高的的问题： 
* 生成实例当前诊断报告，推荐使用该方式。
* 终止问题查询。 
* 利用SQL优化工具或手动优化SQL语句，减少查询的复杂度和I/O需求。

### 如何连接RDS数据库
连接RDS数据库的方式有公网连接和内网连接两种，建议使用内网连接的方式保证传输速率和安全性。
* RDS在切换内外网的过程中会出现服务器跟RDS的连接断开，同时切换内外网会导致IP地址变更，切换完成以后请及时更新程序中的连接地址。
* 在高安全模式下，RDS支持仅内网连接、仅外网连接、内外网同时连接。
* 在标准模式下、RDS实例不支持内网、外网同时连接。

### Polar
云原生关系型数据库PolarDB是阿里巴巴自主研发的下一代云原生关系型数据库，100%兼容MySQL、PostgreSQL、高度兼容Oracle语法。

### 数据库自洽服务DAS
数据库自治服务DAS提供Redis自动扩容功能
