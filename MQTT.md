### 介绍

MQTT（消息队列遥测传输）是一种轻量级的物联网通信协议，基于发布/订阅模式，适用于低带宽和高延迟的网络环境。它的主要特点包括：

低开销：MQTT设计精简，适合资源有限的设备和网络环境。 

实时消息传递：能够为连接的远程设备提供可靠的实时消息服务。 

广泛应用：常用于物联网、M2M通信、消息推送和智能设备等领域。 

核心组件：包括MQTT代理和客户端，代理负责消息的分发，客户端则发布和订阅消息。 


MQTT因其高效性和灵活性，成为物联网应用中非常流行的选择。 

### Broker代理服务器

在MQTT协议中，Broker（代理服务器）是MQTT通信的核心中间件，负责消息的路由和管理。以下是详细说明:

核心职责

- 消息中转:接收客户端发布的消息，并根据订阅关系转发到目标客户端。
- 会话管理:维护客户端连接状态、订阅信息和QoS消息的传输进度。
- 主题管理:维护主题树结构，处理订阅/取消订阅请求。
- 持久化:对QoS 1/2的消息进行持久化存储，确保可靠传输。


发布者 ->(发布消息) -> 代理 ->（推送消息） 订阅者
                       代理 <-（订阅消息） 订阅者

### QoS

在MQTT协议中，QoS（Quality of Service） 等级是核心机制，用于控制消息的可靠性和传输成本。

QoS 0:最多一次（At most once）

流程:
消息仅传输一次，不等待确认。
客户端 → Broker → 目标客户端（单向传输）

QoS 1:至少一次（At least once）

流程:  
客户端发送消息 → Broker。
Broker发送 PUBACK 确认。
客户端收到 PUBACK 后删除消息副本。


QoS 2:恰好一次（Exactly once）

流程（四步握手）:  
客户端发送 PUBLISH → Broker。
Broker回复 PUBREC（接收确认）。
客户端发送 PUBREL（释放确认）。
Broker回复 PUBCOMP（完成确认）。

### 功能类型

| 所属功能     | 控制报文类型 | 描述                     | 报文流动方向         |
|-------------|-------------|--------------------------|---------------------|
| 建立连接     | CONNECT     | 连接服务端               | 客户端到服务器       |
|             | CONNACK     | 确认连接请求             | 服务器到客户端       |
| 消息发布     | PUBLISH     | 发布消息                 | 两个方向都允许       |
|             | PUBACK      | 发布确认                 | 两个方向都允许       |
|             | PUBREC      | 发布收到(Qos2,第一步)    | 两个方向都允许       |
|             | PUBREL      | 发布释放(Qos2,第二步)    | 两个方向都允许       |
|             | PUBCOMP     | 发布完成(Qos2,第三步)    | 两个方向都允许       |
| 主题订阅     | SUBSCRIBE   | 订阅主题                 | 客户端到服务器       |
|             | SUBACK      | 订阅确认                 | 服务器到客户端       |
| 取消订阅     | UNSUBSCRIBE | 取消订阅                 | 客户端到服务器       |
|             | UNSUBACK    | 取消订阅确认             | 服务器到客户端       |
| 心跳维护     | PINGREQ     | 心跳请求                 | 客户端到服务器       |
|             | PINGRESP    | 心跳响应                 | 服务器到客户端       |
| 断开连接     | DISCONNECT  | 断开连接                 | 客户端到服务器       |

### 单包大小

协议允许:最大单包数据为 256MB（3.1.1）/512MB（5.0）。
实际限制:由Broker配置、客户端内存和网络MTU决定，通常 1KB~1MB。
嵌入式推荐:单包不超过 10KB，并启用分片和压缩优化。

### MQTT报文格式详解

MQTT协议的所有报文（Packet）均遵循以下标准格式，由固定头（Fixed Header）、可变头（Variable Header）和载荷（Payload）三部分组成。

#### **固定头（Fixed Header）**

每个MQTT报文必须包含固定头，结构如下:

| **字段**         | **长度** | **说明**                                              |
| -------------- | ------ | --------------------------------------------------- |
| **报文类型**       | 4 bits | 标识报文类型（如 `CONNECT=1`, `PUBLISH=3`等），共16种类型。         |
| **标志位（Flags）** | 4 bits | 类型相关标志，如 `PUBLISH` 报文中包含 `DUP`、`QoS`、`RETAIN` 等控制位。 |
| **剩余长度**       | 1~4字节  | 可变头 + 载荷的总字节数（动态编码，最大支持256MB数据）。                    |
#### 可变头（Variable Header）

部分报文类型需要可变头，内容与类型相关:

| **报文类型**    | **可变头内容**                                                    |
| ----------- | ------------------------------------------------------------ |
| `CONNECT`   | 协议名（`MQTT`）、协议版本（`0x04`/`0x05`）、连接标志（`Clean Session`等）、心跳间隔。 |
| `PUBLISH`   | 主题名（Topic Name）和报文标识符（Packet ID，仅QoS 1/2需要）。                 |
| `SUBSCRIBE` | 报文标识符（Packet ID）和订阅主题列表。                                     |

#### 载荷（Payload）

部分报文类型需要载荷:

|**报文类型**|**载荷内容**|
|---|---|
|`PUBLISH`|实际消息内容（二进制或文本）。|
|`SUBSCRIBE`|订阅的主题列表及对应的QoS等级。|
|`CONNECT`|客户端ID、用户名、密码等（可选）。|

##  MQTT-开源库 

[paho.mqtt.embedded-c](https://github.com/eclipse-paho/paho.mqtt.embedded-c/tree/master)


---
### MQTTLinux.c, MQTTFreeRTOS.c-网络层

- `NetworkInit`: 初始读写接口, 如linux_read，linux_write设置, 也可以再抽象一层, 通过回调传入
- `NetworkConnect`: 与平台相关的tcp链接操作, 也可以再抽象一层, 通过回调传入
- `xx_write`: 写数据, 也可以再抽象一层, 通过回调传入实际平台相关写接口
- `xx_write`: 读数据, 也可以再抽象一层, 通过回调传入实际平台相关读接口
- `TimerInit`: 时间创建回调time_creator，初始化timer，赋值为当前时间 
- `TimerIsExpired`: 时间是否过期回调time_isexpired，和当前时间比较，比如用于sendPacket超时判断
- `TimerCountdown`: 追加时间s回调time_countdown，比如用于设定sendPacket超时时间
- `TimerCountdownMS`: 追加时间ms回调time_countdownms
- `TimerLeftMS`: 计算与当前时间的时间差time_leftms
- `MutexInit`: 创建锁, 也可以再抽象一层, 通过回调传入实际平台相关锁创建接口
- `MutexLock`: 加锁, 也可以再抽象一层, 通过回调传入实际平台相关加锁接口
- `MutexUnLock`: 解锁, 也可以再抽象一层, 通过回调传入实际平台相关解锁接口

---
### MQTTClient.c

- `MQTTSubscribe`: 基于topic向Broker订阅（`MQTTSerialize_subscribe`，`sendPacket`），传入`messageHandler`，即收到订阅数据后的回调，注册到`c->messageHandlers`中
- `MQTTClientInit`: 初始化ping_timer，connect_timer，send_mutex，以及send/recv buf和buf_size等
- `MQTTClientFini`: 反初始化
- `decodePacket`:用于解析报文头中的剩余长度字段（包头第2字节），确定后续数据的字节数 
    - **示例**:若收到字节序列 `0x82 0x01`:
    - 第一字节 `0x82`:`(0x82 & 0x7F) * 1 = 2`
    - 第二字节 `0x01`:`(0x01 & 0x7F) * 128 = 128`
    - 总和:`2 + 128 = 130` 字节。
- `MQTTPacket_encode`: 将剩余长度存储到buf中
- `readPacket`: 读取一个mqtt报文（Packet），调用mqttread（1字节），decodePacket（读取剩余长度字节），MQTTPacket_encode，mqttread（读取剩余报文）
- `isTopicMatched`: 判断topic是否匹配
- `deliverMessage`: 调用`MQTTPacket_equals`，`isTopicMatched`，匹配则调用对应的messageHandler
- `keepalive`: mqtt保活，调用TimerIsExpired判断是否过期，过期则调用`MQTTSerialize_pingreq`，`sendPacket`发送心跳请求进行保活
- **`cycle`**: mqtt消息接收接口, 调用`readPacket`读取消息类型, 并基于类型进行处理; 另外调用`keepalive`进行保活
- `MQTTYield`: 设定超时时间, 调用`cycle`
- `waitfor`: 基于输入的消息类型进行读取数据, 用在等待`MQTTConnect`接口的`CONNACK`, `MQTTSubscribe`接口的`SUBACK`, `MQTTUnsubscribe`接口的`UNSUBACK`
- `MQTTConnect`: mqtt connect操作
- `MQTTSubscribe`: mqtt subscribe操作
- `MQTTUnsubscribe`: mqtt unsubscribe操作
- `MQTTPublish`: mqtt publish操作
- `MQTTDisconnect`: mqtt disconnect操作

---

