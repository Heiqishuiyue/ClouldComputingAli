# 地域相关
* 安全组可以跨可用区。
* ECS只能挂载同可用区的磁盘/云盘。
* DDoS原生防护实例的地域必须与需要防护的阿里云公网IP资产地域一致。
# 经典网络的产品相关问题
* VPN
* EIP不支持绑定经典网络的ECS。
* 高速通道不支持经典网络。
* 伸缩组不支持ECS网络混搭。
* CLB不支持混挂。对于专有网络CLB只能将与CLB实例所属同一个专有网络ECS实例添加到同一个服务组内。
* 经典网络类型CLB支持专有网络VPC类型后端服务器。
* RDS是支持经典网络的。
* ACK集群不支持经典网络，仅支持专有网络。
# VPC
* 一个公网NAT网关最多绑定20EIP
* 同一个VPC可以创建5个公网NAT网关。
# 安全组
* 实例上挂载的弹性网卡中，辅助网卡可以和实例加入不同的安全组。
* 实例可以关联多个安全组，多个安全组只能是普通安全组或企业安全组的一个。
* 实例至少加入一个安全组。
* 默认放行：ICMP协议、SSH 22端口、RDP 3389端口，授权对象为0.0.0.0/0。不包括HTTP 80端口。
* 跳板机和生产ECS应该放在不同的安全组。
* 安全组内网络隔离是网卡级别的隔离，不是实例级别。
* 默认安全组是普通安全组，组内网络不隔离。
* 安全组的规则不支持Mac地址。
* ECS加入安全组数量有上限，最多5个。
* ECS添加到安全组不需要任何操作立即生效。
# ECS
* ECS属于IaaS基础设施服务。
* 无需额外的开发，支持自动备份。但注意，IDC也可自动备份，但须要开发。
* 内置主流的操作系统。
* ECS不适合海量数据分析的工作。
* 不支持自行挂载本地盘。
* 实力规格可以分为企业型和共享型。
* 系列 II 较系列 I 进行了硬件升级，采用 Haswell CPU、DDR4 内存，并默认为 I/O 优化实例，同时增加了一些新的指令集，使整数和浮点运算的性能翻倍，整体计算能力更强。但是不支持实例系列 I 与实例系列 II 之间可以互相升降配。
* 状态中没有已删除。
* 短时升配功能支持最短升配间隔为2小时，每次支持最短升配2小时，按小时单价计费，升级过程不会中断业务。
* AccessKeyId和AccessKeySecret阿里云账户RAM鉴权代码，并不是登陆ECS的用户名和密码。
* ECS可以多次设置自动释放的时间，并可以取消设置的自动释放时间。实现随时释放按量付费的实例。
* 查看某个地域所有的ECS实例的步骤，最后一步是选择地域。
* ECS实例均为I/O优化型。（有歧义但选择它！）
* ECS控制台不通过快照无法恢复该磁盘对应的数据快副本。
* ECS默认情况下只能绑定一个EIP。
* 修改私网IP需要保证ECS处于已停止的状态。
* 经典网络ECS转为专有网络ECS支持直接迁移，由于可能依赖了其他应用，所以需要使用镜像。
* ECS实例在发生物理机非预期宕机或进行主动运维时，阿里云默认自动重启恢复您的ECS实例，并且支持自定义恢复方式，恢复方式包括自动重启恢复（默认值）、禁止重启恢复以及自动重新部署三种恢复方式。不包括部分重启恢复。
## 计费
* 节省计划支持三种付费方式：全预付、部分预付和0预付。
* ECS升级CPU或内存等配置需要重启才可享受升级后的配置。
# 快照
* ECS到期或手动释放磁盘是，手动创建的快照不会同时释放。
* 对已经配置完成的数据盘进行打快照，然后在购买或升级页面，添加磁盘的地方，选择用快照创建磁盘，选择你要的快照即可。
* 回滚磁盘必须要求云盘必须已经挂载到某台ECS实例上，而且已经**停止实例**。
* 磁盘快照生成的时候，磁盘本身的读写性能不会发生变化。
* 快照应用场景之一：跨可用区迁移磁盘数据。仅部分地区可以跨可用区复迁移数据盘。
* ext3grep和Extundelete都是恢复删除数据的工具。
* 本地快照就是快照极速生成。
# 镜像
* 社区镜像不支持导出、共享和复制。
* 创建不同的地区相同ECS时，要跨地区复制镜像。
* 不同可用区复制镜像，不同账号之间使用共享镜像。
* 自定义镜像名称和描述不能为空。
# NAS
* NAS备份的文件会存储到混合运备份HBR。
* 通用型NAS支持NFS v3和NFS v4.0协议。具体协议版本可以在挂载时通过参数配置决定。
建议您使用NFS v3。如果需要多个计算节点同时修改同一个文件，则需要使用NFS v4.0挂载以支持文件锁功能。
* 如果需要多个计算节点同时修改同一个文件，则需要使用NFS v4.0挂载以支持文件锁功能。
# 本地盘
* 本地盘不支持的操作有：单独创建全新本地盘、使用快照创建本地盘、挂载本地盘、单独卸载并释放本地盘、扩容本地盘、重新初始化本地盘、**为本地盘创建快照**、使用快照回滚本地盘。
# OSS 
* OSS的跨区域复制功能可以在远距离的OSS数据中心之间复制数据，以满足合规性要求所规定的数据需要跨一定距离保存一份副本。
* 使用工具管理OSS资源时,OSS提供图形化工具、命令行工具、文件挂载工具、FTP工具等方便管理OSS资源。
* 自建存储也支持备份，不是OSS有点。
* 存储空间是用户用于存储对象（Object）的容器，所有的对象都必须隶属于某个存储空间。存储空间具有各种配置属性，**包括地域、访问权限、存储类型**等。
* 防盗链、ACL和自定义域名绑定可以通过Bucket属性设置。
* Bucket名称全局唯一。
* OSS支持使用对象标签（Object Tagging）对存储空间（Bucket）中的文件（Object）进行分类，您可以针对相同标签的Object设置生命周期规则、访问权限等。
* OSS上传方式：简单上传、表单上传、分片上山、断代续传、追加上传。
* OSS删除的方式：生命周期。
* 通过工具管理OSS：图形化管理工具ossbrowser、命令行管理工具ossutil、FTP管理工具ossftp和bucket挂载工具ossfs。
* 通过API/SDK的地方是设置文件夹的时候可以指定object对应的key值包含前端的目录即可实现模拟创建文件夹。
* 回源分为镜像回源和重定向两种，可以满足您对于数据热迁移、特定请求的重定向等需求。
* 客户端进行表单直传到OSS时，需要为Bucket设置跨域规则以支持Post方法。
* 基于 OSS 并使用 MaxCompute 构建 PB 级数据仓库。 通过 MaxCompute 对 OSS 上的海量数据进行分析,将您的大数据分析工作效率提升至分钟级,帮助您更高效、更低成本的挖掘海量数据价值。
* 上传：内网，外网都不收取流量费用；下载：内网不收取流量费用，外网收取流量费用。所以过公网IP下载OSS文件，本地PC机下载OSS文件都是收取流量费用的。
* ossimport是一个命令行工具，用于将本地文件系统中的文件或其他云存储服务中的文件批量导入到阿里云OSS中。因此，它不能直接用于下载OSS中的文件夹。
* Copy Object实现设置Object头。
* OSS域名绑定（CNAME）功能目前仅支持OSS以三级域名访问方式进行绑定。即您的OSS访问方式为（Bucket name）.${region}.aliyuncs.com。OSS cname绑定的域名不是必须经过阿里云备案而是必须经过工信部的备案。
# 图片处理
* 原图大小不能超过20MB。
* 目前阿里云OSS服务支持通过三种方式处理图片：为图片URL添加参数进行单次处理、使用图片样式对不同图片进行相同处理，或使用OSS SDK对图片进行处理。
* 图片格式只能是：JPG、PNG、BMP、GIF、WebP、TIFF。
* 调用resize，默认是不允许放大。难度：难。
* 一个用户最多拥有10个Channel。
* 每个Channel中存放的数量没有限制。
* OSS产品的费用的账单组成：存储费用，流量费用，请求费用、数据处理费、对象标签费用、传输加速费用。
*  存储费用支持包年包月、存储容量单位包SCU、按量付费。

# OSS安全
* Bucket Policy来制作白名单。
* Bucket ACL默认权限是私有读写。
* OSS高防每个实例最多绑定同个地域下10个Bucket。
* OSS高防默认不对Bucket关联的自定义域名进行防护。
* 违规文件的冻结方式：设为私有、删除和移到其他目录下。
* 用户可以针对Bucket设置基于时间的合规保留策略。当策略锁定后，用户可以在Bucket中上传和读取Object，但是在Object的保留时间到期之前，任何用户都无法删除Object和策略。Object的保留时间到期后，才可以删除Object。
* Object ACL是Object级别的权限访问控制。目前有四种访问权限：private(私有读写)、public-read-write(公共读写)、public-read(公共读，私有写)、default(默认权限)即继承Bucket。
* 目前控制台针对私有bucket的获取URL链接签名操作做了优化，使用的AccessKey是临时生成的密钥对，主账号用户最长有效时间是64800秒（18小时），RAM子账号用户以及sts用户最长有效时间是3600秒（1小时），超时后链接就无法访问了。
* 用户只有通过URL签名或者匿名访问Object时，才会做防盗链验证。请求Header中有Authorization字段时，不会做防盗链验证。
# RDS
* 单表最大记录不超过2000万条。
* RDS MySQL对实例变更：
    * 必须是运行中实例
    * 支持升配和降配
    * 连接地址不会变化
    * 无需手动重启实例，支持存储变更。
# CDN
* CDN是有调度系统、链路质量系统、缓存系统、支持服务系统组成。
* 回源需收费。
* 动静态资源混杂适用全站加速CDN。
* 源站信息可选：源站域名、函数式计算域名和OSS域名。
# 伸缩组
* 伸缩组内不限制ECS实例的数量。
* 伸缩组内只能有一项生效的实例配置。
# CLB/CLB
* 结合阿里云日志服务，通过仪表盘巡检, 分析负载均衡的响应时间 
* 公网SLB和ECS是通过内网连接，内网不收费。所以，公网SLB和ECS之间的流量费用是免的。   
* 服务监听包含了监听端口、负载均衡策略以及健康检查配置。
* * 如果需要为伸缩组关联负载均衡实例，请确保满足以下条件：
    *   您持有一个或多个处于运行中状态的负载均衡实例。
    *   负载均衡实例和伸缩组必须位于同一地域。
    *   如果负载均衡实例和伸缩组的网络类型均为专有网络，则必须位于同一专有网络。
    *  当负载均衡实例的网络类型为经典网络，伸缩组的网络类型为专有网络时，如果负载均衡实例的后端服务器组中包含专有网络ECS实例，该ECS实例必须与伸缩组位于同一专有网络。
    * 负载均衡实例配置至少一个监听。
    * 负载均衡实例必须开启健康检查。
