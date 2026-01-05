![[RabbitMQ.png]]
# 概述

## 简介
- **定义**
	- RabbitMQ 是一个开源的消息代理软件（消息队列），用于在分布式系统中存储和转发消息。
	- 基于 AMQP（高级消息队列协议）协议，支持多种编程语言和平台。
- **核心特点**
	- **高可用性**：支持集群和故障转移。
	- **可扩展性**：通过集群和分片实现高吞吐量。
	- **多协议支持**：支持 AMQP、STOMP、MQTT 等协议。
	- **跨平台**：支持 Java、Python、Go、Node.js 等多种语言客户端。
	- **管理界面**：提供 Web 管理界面和命令行工具，便于监控和管理。
- **应用场景**
	- 异步任务处理（如邮件发送、日志记录）。
	- 分布式系统解耦。
	- 消息驱动的微服务架构。
	- 实时数据流处理。
## 需求
- 系统解耦
	- **问题**：在传统系统中，服务之间通过直接调用（如 HTTP 或 RPC）通信，导致强耦合。如果一个服务不可用，可能导致整个系统故障。
	- **RabbitMQ 的解决方案**：
		- 通过消息队列，生产者（Producer）和消费者（Consumer）无需直接交互，生产者只需将消息发送到队列，消费者异步处理。
		- 系统模块之间通过消息传递，降低耦合度，提高系统的灵活性和可维护性。
		- 例如，一个电商系统中的订单服务可以将订单信息发送到队列，库存服务和支付服务异步从队列中获取数据，各自独立处理。
- 异步处理
	- **问题**：某些任务（如发送邮件、生成报表、处理大文件）耗时较长，如果同步执行，会阻塞主流程，降低用户体验。
	- **RabbitMQ 的解决方案**：
		- 将耗时任务放入消息队列，由消费者异步处理，主流程无需等待。
		- 提高系统响应速度，优化用户体验。例如，用户下单后，订单确认邮件可以通过队列异步发送。
- 流量削峰
	- **问题**：在高并发场景（如秒杀活动、突发流量），系统可能因短时间内大量请求而超载，导致服务不可用。
	- **RabbitMQ 的解决方案**：
		- 消息队列作为缓冲区，平滑流量高峰，将请求暂存到队列中，消费者按处理能力逐步消费。
		- 防止系统因突发流量崩溃。例如，秒杀活动中，订单请求先进入队列，消费者按顺序处理，避免数据库直接承受高并发压力。
- 分布式系统通信
	- **问题**：在分布式系统中，不同服务可能运行在不同服务器、不同语言或框架中，通信复杂且易出错。
	- **RabbitMQ 的解决方案**：
		- 支持多种协议（如 AMQP、MQTT、STOMP）和客户端库（如 Java、Python、Go），实现跨平台、跨语言的统一通信。
		- 提供可靠的消息路由机制（如交换机和绑定），确保消息准确送达目标队列。
- 提高系统可靠性
	- **问题**：网络不稳定或服务故障可能导致消息丢失，影响业务一致性。
	- **RabbitMQ 的解决方案**：
		- 支持消息持久化，确保消息在服务器重启后不丢失。
		- 提供 Publisher Confirms 和 Consumer Acknowledgements，确保消息从生产到消费的可靠性。
		- 通过死信队列（Dead Letter Queue）处理无法投递的消息，防止数据丢失。
## 对比

| **特性**      | **RabbitMQ**                                                           | **Apache Kafka**                                           | **ActiveMQ**                                        | **Redis**                                 |
| ----------- | ---------------------------------------------------------------------- | ---------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------- |
| **架构模型**    | 消息代理（Message Broker），基于 AMQP，支持复杂路由，推模型（Push-based）。                   | 分布式事件流平台，基于日志（Log-based），拉模型（Pull-based）。                  | 消息代理，基于 JMS，支持点对点和发布/订阅，推模型。                        | 轻量级键值存储，支持简单队列（如 List 和 Pub/Sub），推模型。     |
| **编程语言**    | Erlang                                                                 | Java 和 Scala                                               | Java                                                | C                                         |
| **协议支持**    | AMQP、MQTT、STOMP、HTTP（通过插件）                                             | Kafka 协议（自定义），支持 HTTP 和 gRPC（通过扩展）                         | JMS、AMQP、STOMP、MQTT、OpenWire                        | Redis 协议（RESP）                            |
| **消息传递模型**  | 智能代理/哑消费者（Smart Broker/Dumb Consumer），消息由交换机路由到队列。                     | 哑代理/智能消费者（Dumb Broker/Smart Consumer），消费者控制消息读取位置。         | 智能代理/哑消费者，类似 RabbitMQ。                              | 简单发布/订阅或列表队列，无复杂路由。                       |
| **消息持久化**   | 支持队列和消息持久化，消息默认存储在内存，可配置存储到磁盘。                                         | 消息持久化到磁盘，支持长期存储和重放，基于日志结构。                                 | 支持持久化，消息可存储到磁盘或数据库（如 JDBC）。                         | 支持持久化（RDB 和 AOF），但队列数据通常在内存中，持久化需额外配置。    |
| **性能（吞吐量）** | 中等，约 4K-10K 消息/秒，集群下可达百万，队列积压可能导致性能下降。                                 | 高，单节点可达百万消息/秒，适合大数据量和高吞吐场景。                                | 中等，约 2K-10K 消息/秒，性能低于 Kafka。                        | 高，单机可达数十万消息/秒，但受限于内存和简单功能。                |
| **延迟**      | 低延迟，适合实时消息传递，推模型优化快速分发。                                                | 稍高延迟，拉模型适合批量处理，需消费者主动拉取。                                   | 低延迟，适合实时场景，但不如 RabbitMQ 灵活。                         | 极低延迟，适合简单、高速场景，但功能有限。                     |
| **可扩展性**    | 支持集群和镜像队列，扩展性良好，但管理复杂。                                                 | 高度可扩展，分布式分区日志架构，适合大规模部署。                                   | 支持集群，扩展性中等，管理较复杂。                                   | 依赖 Redis Cluster，扩展性有限，队列功能简单。            |
| **可靠性**     | 高，支持持久化、确认机制（ACK）、死信队列。                                                | 高，支持数据复制、持久化和日志重放，依赖 ZooKeeper（早期版本）。                      | 高，支持持久化和事务，但故障恢复较慢。                                 | 中等，持久化需配置，队列功能无复杂可靠性机制。                   |
| **消息路由**    | 灵活，支持 Direct、Topic、Fanout、Headers 交换机，复杂路由规则。                          | 基于主题（Topic）和分区（Partition），路由较简单但高效。                        | 支持点对点和发布/订阅，路由灵活性低于 RabbitMQ。                       | 无复杂路由，仅支持简单发布/订阅或列表队列。                    |
| **消息重放**    | 有限，需通过死信队列或生产者重发，Stream Queues（3.9+）支持类似功能。                            | 支持，日志结构允许消费者从任意偏移量重放消息。                                    | 有限，需额外配置（如重发机制）。                                    | 不支持，消息消费后即移除。                             |
| **安全性**     | 支持 TLS、LDAP、OAuth2 等认证，权限管理细粒度。                                        | 支持 TLS、JAAS、SASL，权限管理强大但配置复杂。                              | 支持 SSL、JAAS，安全性中等。                                  | 支持 TLS 和简单密码认证，安全性较弱。                     |
| **客户端支持**   | Java、Python、Go、Node.js、Ruby、PHP 等，语言支持广泛。                              | Java、Python、Go、Node.js、C++ 等，生态系统强大（如 Kafka Connect）。      | Java、Python、C++、.NET 等，JMS 客户端为主。                   | Java、Python、Go、Node.js 等，客户端丰富但队列功能简单。    |
| **生态与集成**   | 插件丰富（如 Shovel、Federation），支持与数据库、监控工具集成。                               | 强大生态（如 Kafka Connect、Streams、ksqlDB），支持广泛数据源。              | 支持多种协议，集成能力中等，生态较小。                                 | 简单集成，适合轻量级应用，与复杂系统集成能力有限。                 |
| **管理与监控**   | 内置 Web 管理界面、rabbitmqctl，支持 Prometheus 集成。                              | 无内置管理界面，依赖第三方工具（如 Confluent Control Center）。               | 内置 Web 控制台，支持 JMX 监控，管理工具较简单。                       | 简单命令行工具和第三方 GUI，监控功能有限。                   |
| **适用场景**    | - 复杂路由（如微服务通信）  <br>- 低延迟任务（如订单处理）  <br>- 异步任务队列（如邮件发送）  <br>- 点对点消息传递 | - 高吞吐数据流（如实时日志、传感器数据）  <br>- 长期存储和重放（如事件溯源）  <br>- 大规模数据管道 | - 传统企业集成（如 JMS 应用）  <br>- 中小规模消息队列  <br>- 混合协议通信    | - 简单队列任务  <br>- 高速缓存和临时消息  <br>- 轻量级发布/订阅 |
| **优缺点**     | **优点**：灵活路由、低延迟、易用性高、管理界面友好。  <br>**缺点**：吞吐量较低，集群管理复杂。                 | **优点**：高吞吐、可扩展、支持消息重放。  <br>**缺点**：配置复杂，延迟稍高。              | **优点**：JMS 支持、易于传统企业集成。  <br>**缺点**：性能和扩展性不如 Kafka。 | **优点**：极低延迟、简单易用。  <br>**缺点**：功能有限，持久化较弱。 |
| **典型案例**    | 电商订单处理、微服务通知、异步任务（如 Celery）。                                           | 实时数据分析、日志聚合、流处理（如网站行为跟踪）。                                  | 企业消息集成、银行交易系统。                                      | 实时聊天、简单任务队列、缓存。                           |

# 开始

## 安装




## 使用





# 概念
## 核心概念
### 核心组件
1. **Producer（生产者）**
	- **定义**：生产者是发送消息的应用程序或服务，负责将消息发布到 RabbitMQ 的交换机。
	- **职责**：
		- 创建消息，包含消息体（Payload）和元数据（如路由键、属性）。
		- 连接到 RabbitMQ 服务器，通过通道（Channel）发送消息。
	- **关键点**：
		- 生产者不直接将消息发送到队列，而是发送到交换机，由交换机根据路由规则分发。
		- 支持同步（Publisher Confirms）或异步发送消息。
	- **示例场景**：Web 应用将用户注册信息发送到 RabbitMQ，供异步处理（如发送欢迎邮件）。
2. **Consumer（消费者）**
	- **定义**：消费者是接收并处理消息的应用程序或服务，从队列中获取消息。
	- **职责**：
		- 订阅特定队列，监听消息到达。
		- 处理消息后发送确认（ACK）或拒绝（NACK）。
	- **关键点**：
		- 支持自动确认（Auto-ACK）或手动确认，手动确认更可靠。
		- 可设置预取值（Prefetch Count）控制未确认消息数量，防止消费者过载。
	- **示例场景**：邮件服务从队列中获取注册信息，发送邮件后确认消息。
3. **Queue（队列）**
	- **定义**：队列是存储消息的缓冲区，遵循 FIFO（先进先出）原则。
	- **属性**：
		- **名称**：队列的唯一标识。
		- **持久化（Durable）**：设置 durable=True，队列在服务器重启后仍然存在。
		- **独占（Exclusive）**：队列仅对单一连接可见，连接关闭后删除。
		- **自动删除（Auto-Delete）**：最后一个消费者断开连接后，队列自动删除。
		- **TTL（存活时间）**：设置队列或消息的过期时间。
	- **关键点**：
		- 队列必须绑定到交换机才能接收消息。
		- 支持多个消费者，消息通过轮询（Round-Robin）分发。
	- **示例场景**：任务队列存储待处理的订单信息，消费者按顺序处理。
4. **Exchange（交换机）**
	- **定义**：交换机接收生产者的消息，并根据绑定规则将消息路由到一个或多个队列。
	- **类型**：
		- **Direct Exchange**：
			- 根据消息的路由键（Routing Key）精确匹配队列的绑定键。
			- 适合点对点通信。
			- 示例：路由键为 order.process，只发送到绑定了该键的队列。
		- **Topic Exchange**：
			- 根据路由键的模式匹配（如通配符 * 表示一个单词，# 表示零个或多个单词）。
			- 适合发布/订阅模式，支持复杂路由。
			- 示例：路由键 log.error.* 可匹配 log.error.db 或 log.error.api。
		- **Fanout Exchange**：
			- 将消息广播到所有绑定的队列，无需路由键。
			- 适合广播场景。
			- 示例：日志系统将消息发送到所有订阅的队列。
		- **Headers Exchange**：
			- 根据消息的头部属性（Headers）进行路由，而非路由键。
			- 适合基于复杂元数据的路由。
			- 示例：根据消息头 format=pdf 路由到特定队列。
		- **Dead Letter Exchange（死信交换机）**：
			- 处理无法投递的消息（如过期、拒绝或队列满）。
			- 需要配置死信队列（DLQ）和死信交换机（DLX）。
	- **属性**：
		- **持久化（Durable）**：设置 durable=True，交换机在服务器重启后存在。
		- **自动删除（Auto-Delete）**：最后一个绑定解除后，交换机自动删除。
	- **关键点**：
		- 交换机不存储消息，仅负责路由。
		- 必须通过绑定（Binding）与队列关联。
5. **Binding（绑定）**
	- **定义**：绑定是交换机与队列之间的关联，定义了消息的路由规则。
	- **绑定键（Binding Key）**：
		- 在 Direct 和 Topic 交换机中，绑定键与消息的路由键匹配。
		- Fanout 交换机无需绑定键。
		- Headers 交换机使用头部属性匹配。
	- **关键点**：
		- 一个队列可绑定到多个交换机，一个交换机可绑定到多个队列。
		- 绑定规则决定了消息的分发路径。
	- **示例**：队列 Q1 绑定到 Direct Exchange 的绑定键 error，只接收路由键为 error 的消息。
6. **Message（消息）**
	- **定义**：消息是生产者发送到 RabbitMQ 的数据单元，包含消息体和元数据。
	- **消息体（Payload）**：
		- 实际传输的数据，通常为 JSON、XML 或二进制格式。
	- **元数据（Properties）**：
		- **Content Type**：消息内容的 MIME 类型（如 application/json）。
		- **Message ID**：消息的唯一标识。
		- **Timestamp**：消息创建时间。
		- **Priority**：消息优先级（0-9，需队列支持）。
		- **Expiration**：消息过期时间，过期后被丢弃或移到死信队列。
		- **Delivery Mode**：持久化（2）或非持久化（1）。
	- **关键点**：
		- 消息持久化需结合队列和交换机的持久化设置。
		- 支持消息压缩和加密以提高效率和安全性。
### 消息传递流程
1. **生产者**连接 RabbitMQ，声明交换机和通道，发送消息到指定交换机。
2. **交换机**根据路由规则（路由键或头部属性）将消息分发到绑定的队列。
3. **队列**存储消息，等待消费者处理。
4. **消费者**订阅队列，获取消息，处理后发送确认（ACK）或拒绝（NACK）。
5. 如果消息无法投递（队列不存在、过期等），可配置死信交换机处理。
![[RabbitMQ-1.png]]
### 消息可靠性机制
- **Publisher Confirms**
	- 生产者发送消息后，等待 RabbitMQ 确认消息已送达交换机或队列。
	- 支持同步（等待确认）和异步（回调函数）模式。
	- 提高消息发送的可靠性，防止丢失。
- **Consumer Acknowledgements**
	- **自动确认**：消息送达消费者后立即认为已处理，适合低可靠性场景。
	- **手动确认**：消费者处理完消息后显式发送 ACK/NACK。
		- ACK：确认消息已成功处理，从队列中移除。
		- NACK：拒绝消息，可选择重新入队列或丢弃。
	- 手动确认结合死信队列可处理失败消息。
- **死信队列（Dead Letter Queue, DLQ）**
	- **触发条件**：
		- 消息被消费者拒绝（NACK）且不重新入队列。
		- 消息过期（TTL）。
		- 队列达到最大长度，溢出消息。
	- **实现**：
		- 配置队列的 `x-dead-letter-exchange` 和 `x-dead-letter-routing-key`。
		- 创建死信交换机和死信队列接收无法投递的消息。
	- **用途**：记录失败消息、触发重试机制或报警。
- **持久化**
	- **交换机持久化**：设置 `durable=True`，确保重启后交换机存在。
	- **队列持久化**：设置 `durable=True`，确保重启后队列存在。
	- **消息持久化**：设置 `delivery_mode=2`，确保消息存储到磁盘。
	- **注意**：持久化会降低性能，需权衡可靠性与吞吐量。




# 配置





# 思考




# 附录
> [!website]
    >1. [RabbitMQ 官网](https://www.rabbitmq.com/)

> [!code]
    >2. [RabbitMQ - Github](https://github.com/rabbitmq/rabbitmq-website)

> [!video]
    > 

> [!doc]
    >3. [RabbitMQ 官方文档](https://www.rabbitmq.com/docs)







# 概述

## 简介

RabbitMQ是一个开源的消息代理系统（交换器）。使用了高级消息队列协议（AMQP）来在**分布式系统**中实现可靠的消息传递。

官网：[https://www.rabbitmq.com/](https://www.rabbitmq.com/)

## 核心

- **消息代理（Message Broker）**
    
    RabbitMQ是一个消息代理，通过存储和转发消息，将消息从生产者（Producer）传递到消费者（Consumer），解耦了系统之间的通信。生产者与消费者不必直接通信，而是借助消息代理完成消息的传递。
    
- **AMQP协议**
    
    AMQP（Advanced Message Queuing Protocol）是一种应用层协议，定义了消息的格式、传输方式和消息路由机制，支持发布/订阅模式。RabbitMQ最早设计时使用的是AMQP 0.9.1版本，目前对该版本有良好的支持。
    
- **消息（Message）**
    
    消息是生产者发送给RabbitMQ的一段数据内容，由`Message Body`（消息主体）和`Message Properties`（消息属性）组成，用于传递业务数据。
    
- **队列（Queue）**
    
    队列是RabbitMQ存储消息的地方，类似于一个消息容器。消息进入队列等待消费者消费，支持先入先出（FIFO）模式。
    
- **交换器（Exchange）**
    
    RabbitMQ通过交换器将消息路由到相应的队列。每个交换器通过绑定（Binding）规则决定消息路由路径。交换器的类型包括`direct`、`topic`、`fanout`和`headers`。交换器只负责转发消息，没有保存消息的能力，所以一旦交换器找不到符合条件的队列，消息就会丢失。
    
- **绑定（Binding）**
    
    绑定是交换器与队列之间的连接关系，通过绑定，交换器可以将消息发送到特定的队列。
    

## 工作流程

- **生产者发布消息到交换器**
    
    生产者将消息发送给交换器，交换器根据绑定规则决定该消息路由到哪些队列中。
    
- **交换器根据路由键（Routing Key）分发消息**
    
    交换器使用路由键判断消息应发送到的队列。路由键与队列的绑定键相匹配时，消息将被放入该队列。
    
- **队列存储消息**
    
    被交换器路由到的消息进入队列中，等待消费者读取。
    
- **消费者从队列中获取消息**
    
    消费者从队列中消费消息，RabbitMQ支持多种消费方式，如推送模式和拉取模式。消息处理完毕后，消费者可以确认消息已被正确处理（acknowledgment）。
    

## 交换器类型

- **Direct Exchange（直接交换器）**
    
    使用完全匹配的路由键。生产者发送的消息有特定的路由键，交换器将消息路由到具有相同绑定键的队列中。
    
    **示例**：若消息的路由键为`error`，则该消息将会被转发到绑定键为`error`的队列。
    
- **Fanout Exchange（扇出交换器）**
    
    扇出交换器将消息广播到所有与之绑定的队列中，不使用路由键，适合用于广播通知场景。
    
- **Topic Exchange（主题交换器）**
    
    主题交换器通过模式匹配的方式将消息路由到队列中。路由键可以包含通配符`*`（匹配一个词）和`#`（匹配零个或多个词），适合按主题订阅的场景。
    
    **示例**：若路由键为`error.*`，可以匹配到类似`error.user`、`error.system`等队列。
    
- **Headers Exchange（头部交换器）**
    
    头部交换器使用消息的属性作为路由条件，通过`headers`字段指定的键值对来匹配，将消息路由到相应的队列。通常用于消息的属性包含复杂条件时。
    

## 使用场景

- **任务队列**
    
    RabbitMQ支持任务分发，将复杂的任务拆解成多个子任务，通过队列发送给多个消费者，实现分布式处理。
    
- **发布/订阅模式**
    
    生产者将消息发布到交换器中，交换器根据订阅规则分发消息，消费者可以从订阅的队列中获取消息。
    
- **负载均衡**
    
    当多个消费者从一个队列中消费消息时，RabbitMQ可以将消息均匀分配给不同的消费者，达到负载均衡的效果。
    
- **延迟消息队列**
    
    RabbitMQ通过插件或死信交换器实现延迟消息功能，可以控制消息延迟一段时间后再被消费者消费，常用于超时通知、定时任务等场景。
    

## 高级功能

- **消息确认（Message Acknowledgment）**
    
    消费者在消费消息后向RabbitMQ发送确认（ack），告知消息已成功处理。若未确认，RabbitMQ会将消息重新入队，避免消息丢失。
    
- **死信队列（Dead Letter Queue, DLQ）**
    
    消息在队列中无法被正常消费时会被发送到死信队列，常用于处理无法消费的异常消息。
    
- **持久化（Durability）**
    
    RabbitMQ支持持久化机制，可以将消息和队列存储到磁盘，保证系统重启后消息不会丢失。
    
- **优先级队列（Priority Queue）**
    
    RabbitMQ允许为消息设置优先级，消费者会优先处理高优先级的消息，适合用在需按优先级顺序处理的场景。
    
- **流量控制（Flow Control）**
    
    RabbitMQ支持流量控制机制，用于在消费者处理能力不足时对流量进行限流，避免系统崩溃。
    
- **延迟插件**
    
    RabbitMQ官方插件`rabbitmq-delayed-message-exchange`可以帮助实现延迟消息功能，为消息设置延迟时间。
    

## 注意事项

- **消息持久化的开销**
    
    开启消息持久化可以提高可靠性，但也会降低吞吐量。在高并发系统中需考虑性能与可靠性的权衡。
    
- **队列积压问题**
    
    若生产者的发送速度大于消费者的消费速度，队列容易积压过多消息，导致内存溢出，可以设置队列的TTL（存活时间）或使用死信队列处理过期消息。
    
- **合理设计路由键**
    
    路由键的设计影响消息的分发效率，应根据实际业务场景合理设计，避免创建大量无效的绑定。
    
- **监控和管理**
    
    RabbitMQ提供Web管理控制台及API接口，可以实时监控队列状态、交换器绑定、消息流量等信息，帮助管理员及时发现并解决问题。
    

## 队列模型

RabbitMQ提供了几种使用该工具的模型：

- **简单队列模型**：简单的生产消费
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/65c6fe6b-653c-4eec-9416-fb7e23a75b3c/image.png)
    
- **工作队列模型**：多个工作者竞争消费者
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/943465bd-3326-4450-84c9-058ba9bb5277/image.png)
    
- **发布订阅模型**：一次向多个消费者发送消息
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/73673c46-4add-4212-aa9b-0ad518cefce8/image.png)
    
- **路由模型**：选择性接收消息
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/02eeeb7c-2d75-494d-8ab7-fc7e06c3dba1/image.png)
    
- **主题模型**：根据模式（主题）接收消息
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/059ec80d-0496-4445-b821-afbf02aa4390/image.png)
    
- **RPC模型**：请求/回复模式
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/0b8c4012-ad85-4b3d-924b-1f14eb8bc17f/image.png)
    

## 基本结构

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/86b84786-b3da-4cd4-9bba-1dba2f65c722/image.png)

# 安装

RabbitMQ 分为 client 和 server，需要先安装 server，在应用程序中使用 client 与之通信

## 单机部署

Docker 部署是一种流行的方式

推荐使用 management 版本，这个版本中带了管理插件，可以在后台可视化管理

```bash
docker run \\
 -e RABBITMQ_DEFAULT_USER=root \\
 -e RABBITMQ_DEFAULT_PASS=root \\
 --name rabbitmq \\
 -p 15672:15672 \\
 -p 5672:5672 \\
 -d \\
 rabbitmq:3.13.7-management
```

- `-e`：环境变量，设置默认的用户名和密码
- `--name`：容器名称
- `-p`：端口映射
- `-d`：后台运行

后台管理地址：[](http://localhost:15672/)[http://localhost:15672](http://localhost:15672)

## 集群部署

TODO

# 使用

## 引入依赖

在 Maven 中可以引入以下三种依赖：

- **`amqp-client`**：适用于不依赖于 Spring 框架情况
- **`spring-boot-starter-amqp`**：适用于简化配置，通过 SpringAMQP 集成 RabbitMQ，内置 `RabbitTemplate`
- **`spring-integration-amqp`**：适用于需要与其他消息中间件集成场景

## 入门案例

入门案例将实现一个不使用 Spring 框架的简单队列模型

- publisher
    
    ```java
    public class SimpleSend {
        private final static String QUEUE_NAME = "simple";
        private final static String USER_NAME = "root";
        private final static String PASSWORD = "root";
    
        public static void main(String[] args) {
            ConnectionFactory factory = new ConnectionFactory();
            /*设置工厂配置，具有默认值*/
            factory.setHost("localhost");
            factory.setUsername(USER_NAME);
            factory.setPassword(PASSWORD);
            /*创建连接对象和通道，实现了AutoCloseable*/
            try (Connection conn = factory.newConnection();
                 Channel channel = conn.createChannel()) {
                /*声明队列*/
                channel.queueDeclare(QUEUE_NAME, false, false, false, null);
                String message = "Hello World";
                channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            } catch (IOException | TimeoutException e) {
                throw new RuntimeException(e);
            }
        }
    }
    ```
    
- subscriber
    
    ```java
        public static void main(String[] args) {
            ConnectionFactory factory = new ConnectionFactory();
            /*设置工厂配置，具有默认值*/
            factory.setHost("localhost");
            factory.setUsername(USER_NAME);
            factory.setPassword(PASSWORD);
            try {
                /*创建连接对象和通道*/
                Connection conn = factory.newConnection();
                Channel channel = conn.createChannel();
                channel.queueDeclare(QUEUE_NAME, false, false, false, null);
                System.out.println("正在等待消息中...");
    
                /*接收回调*/
                DeliverCallback deliverCallback = (consumerTag, delivery) -> {
                    String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
                    System.out.println("[✔️]接收消息：'" + message + "'");
                };
                channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> {
                });
            } catch (IOException | TimeoutException e) {
                throw new RuntimeException(e);
            }
        }
    }
    ```
    

## 简单队列模型

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/65c6fe6b-653c-4eec-9416-fb7e23a75b3c/image.png)

下面将全部使用 SpringAMQP 的方式使用 RabbitMQ

- **yaml**
    
    在发布订阅模块中都需要设置
    
    配置将覆盖默认值
    
    ```yaml
    spring:
      rabbitmq:
        host: localhost
        username: root
        password: root
    ```
    
- **config**
    
    首先启动的是消费者，所以在消费者服务中声明队列
    
    也可以在 RabbitMQ 的后台面板中手动创建队列
    
    ```java
    @Configuration
    public class RabbitMQConfig {
        public static final String QUEUE_NAME = "simple.queue";
    
        // 声明队列
        @Bean
        public Queue queue(){
            return new Queue(QUEUE_NAME);
        }
    }
    ```
    
- **publisher**
    
    首先将依赖中的 SpringTest 的 scope 属性删除
    
    使用方法：`convertAndSend(String *routingKey*, Object *object*)`
    
    ```java
    @SpringBootTest
    public class simpleSendAMQP {
    
        @Autowired
        private RabbitTemplate rabbitTemplate;
    
        @Test
        public void send() {
            String QUEUE_NAME = "simple.queue";
            String message = "Hello World";
            rabbitTemplate.convertAndSend(QUEUE_NAME, message);
        }
    
    }
    ```
    
- **subscriber**
    
    ```java
    @Component
    public class SimpleReceiveAMQP {
    
        @RabbitListener(queues = {RabbitMQConfig.QUEUE_NAME})
        public void receive(String message) {
            System.out.println("[✔️]接收消息：'" + message + "'");
        }
    }
    ```
    

## 工作队列模型

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/943465bd-3326-4450-84c9-058ba9bb5277/image.png)

相对于简单队列，工作队列会有多个消费者监听同一个队列的消息

在有多个消费者时，队列中的消息会以轮流的方式，平均分配给消费者

如果要实现以处理能力进行负载均衡，可以在配置文件中通过简单配置实现

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1 #每次只能获取一条消息，处理完成才能获取下一个消息
```

## 发布订阅模型

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/73673c46-4add-4212-aa9b-0ad518cefce8/image.png)

从这个模型开始将会引入交换器这个概念

生产者将把消息发送给交换器，由交换器决定该把消息交给哪些队列

这个模型对应的交换器为 **Fanout Exchange**（扇出交换器）

会将消息**广播给所有与之绑定的队列**

- config
    
    还是在消费者服务中配置，声明交换机、队列以及它们的绑定关系
    
    ```java
    @Configuration
    public class FanoutConfig {
        public static final String QUEUE_NAME_1 = "fanoutQueue1";
        public static final String QUEUE_NAME_2 = "fanoutQueue2";
        public static final String EXCHANGE_NAME = "fanoutExchange";
    
        /**
         * 声明扇出交换器
         *
         * @return {@code FanoutExchange }
         */
        @Bean
        public FanoutExchange fanoutExchange() {
            return new FanoutExchange(EXCHANGE_NAME);
        }
    
        /**
         * 声明第一个队列
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue fanoutQueue1() {
            return new Queue(QUEUE_NAME_1);
        }
    
        /**
         * 绑定第一个队列
         *
         * @param fanoutExchange 扇出交换
         * @param fanoutQueue1   扇出队列1
         * @return {@code Binding }
         */
        @Bean
        public Binding fanoutBinding(FanoutExchange fanoutExchange, Queue fanoutQueue1) {
            return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
        }
    
        /**
         * 声明第二个队列
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue fanoutQueue2() {
            return new Queue(QUEUE_NAME_2);
        }
    
        /**
         * 绑定第二个队列
         *
         * @param fanoutExchange 扇出交换
         * @param fanoutQueue2   扇出队列2
         * @return {@code Binding }
         */
        @Bean
        public Binding fanoutBinding2(FanoutExchange fanoutExchange, Queue fanoutQueue2) {
            return BindingBuilder.bind(fanoutQueue2).to(fanoutExchange);
        }
    }
    ```
    
- publisher
    
    将消息发送给交换器，将路由键设置为空字符串
    
    使用方法：`convertAndSend(String *exchange*, String *routingKey*, Object *object*)`
    
    ```java
    @SpringBootTest
    public class PublishSendAMQP {
        @Autowired
        private RabbitTemplate rabbitTemplate;
    
        @Test
        public void send() {
            String exchangeName = "fanoutExchange";
            String message = "Hello World!";
            rabbitTemplate.convertAndSend(exchangeName,"", message);
        }
    }
    ```
    
- subscriber
    
    ```java
    @Component
    public class PublishReceiveAMQP {
    
        @RabbitListener(queues = {FanoutConfig.QUEUE_NAME_1})
        public void receive1(String message) {
            System.out.println("[✔️]消费者1接收消息：'" + message + "'");
        }
    
        @RabbitListener(queues = {FanoutConfig.QUEUE_NAME_2})
        public void receive2(String message) {
            System.out.println("[✔️]消费者2接收消息：'" + message + "'");
        }
    }
    ```
    

## 路由模型

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/02eeeb7c-2d75-494d-8ab7-fc7e06c3dba1/image.png)

这个模型对应的交换器是 **Direct Exchange**（直连交换器）

队列在绑定 Direct Exchange 交换器时，需要设置一个 **Routing Key**

在发布者发布消息时，需要携带 Routing Key，交换器根据 Routing Key，将消息发送给有相同 Routing Key 的队列

- config
    
    在声明绑定关系时，声明队列的路由键
    
    ```java
    @Configuration
    public class RoutingConfig {
        public static final String QUEUE_NAME_1 = "directQueue1";
        public static final String QUEUE_NAME_2 = "directQueue2";
        public static final String EXCHANGE_NAME = "directExchange";
    
        /**
         * 声明 DirectExchange 交换器
         *
         * @return {@code DirectExchange }
         */
        @Bean
        public DirectExchange directExchange() {
            return new DirectExchange(EXCHANGE_NAME);
        }
    
        /**
         * 队列1
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue queue1(){
            return new Queue(QUEUE_NAME_1);
        }
    
        /**
         * 绑定第一个队列，使用路由键 blue
         *
         * @param directExchange 直接交换
         * @param queue1         队列1
         * @return {@code Binding }
         */
        @Bean
        public Binding directBinding1(DirectExchange directExchange, Queue queue1) {
            return BindingBuilder.bind(queue1).to(directExchange).with("blue");
        }
    
        /**
         * 绑定第一个队列，使用路由键 yellow
         *
         * @param directExchange 直接交换
         * @param queue1         队列1
         * @return {@code Binding }
         */
        @Bean
        public Binding directBinding2(DirectExchange directExchange, Queue queue1) {
            return BindingBuilder.bind(queue1).to(directExchange).with("yellow");
        }
    
        /**
         * 队列2
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue queue2(){
            return new Queue(QUEUE_NAME_2);
        }
    
        /**
         * 绑定第二个队列，使用路由键 green
         *
         * @param directExchange 直接交换
         * @param queue2         队列2
         * @return {@code Binding }
         */
        @Bean
        public Binding binding3(DirectExchange directExchange, Queue queue2) {
            return BindingBuilder.bind(queue2).to(directExchange).with("green");
        }
    }
    ```
    
- publisher
    
    需要携带路由键
    
    使用方法：`convertAndSend(String *exchange*, String *routingKey*, Object *object*)`
    
    ```java
    @SpringBootTest
    public class RoutingSendAMQP {
        @Autowired
        private RabbitTemplate rabbitTemplate;
    
        @Test
        public void send() {
            String exchangeName = "directExchange";
            String[] routingKeys = {"blue","yellow","green"};
            rabbitTemplate.convertAndSend(exchangeName,routingKeys[0],"blue");
            rabbitTemplate.convertAndSend(exchangeName,routingKeys[1],"yellow");
            rabbitTemplate.convertAndSend(exchangeName,routingKeys[2],"green");
        }
    }
    ```
    
- subscriber
    
    ```java
    @Component
    public class RoutingReceiveAMQP {
    
        @RabbitListener(queues = {RoutingConfig.QUEUE_NAME_1})
        public void receive1(String msg) {
            System.out.println("[✔️]消费者1接收消息：'" + msg + "'");
        }
    
        @RabbitListener(queues = {RoutingConfig.QUEUE_NAME_2})
        public void receive2(String msg) {
            System.out.println("[✔️]消费者2接收消息：'" + msg + "'");
        }
    }
    ```
    

---

上面这种设置绑定关系在配置多个路由键时比较繁琐，但有助于重用和维护

如果只使用一次，也可以直接在消费者中基于注解方式配置

- subscriber
    
    ```java
    @Component
    public class RoutingReceiveAMQP {
    
        @RabbitListener(bindings = @QueueBinding(
                value = @Queue(name = "directQueue1"),
                exchange = @Exchange(name = "directExchange",type = ExchangeTypes.DIRECT),
                key = {"blue","yellow"}
        ))
        public void receive1(String msg) {
            System.out.println("[✔️]消费者1接收消息：'" + msg + "'");
        }
    
        @RabbitListener(bindings = @QueueBinding(
                value = @Queue(name = "directQueue2"),
                exchange = @Exchange(name = "directExchange",type = ExchangeTypes.DIRECT),
                key = {"green"}
        ))
        public void receive2(String msg) {
            System.out.println("[✔️]消费者2接收消息：'" + msg + "'");
        }
    }
    ```
    

## 主题模型

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/059ec80d-0496-4445-b821-afbf02aa4390/image.png)

这个模型与之对应的交换器是 **TopicExchange**（主题交换器）

相对于 DirectExchange，这个交换器的特定是交换器可以通过通配符找到路由键匹配的队列

- `#`：匹配一个或多个词
- `*`：匹配一个词

这里说的一个或多个词是通过点分割的，在实际使用中，路由键是由多个词组成，由点分割

- config
    
    在设置队列路由键时，可以使用通配符
    
    ```java
    @Configuration
    public class TopicsConfig {
        public static final String QUEUE_NAME_1 = "topicQueue1";
        public static final String QUEUE_NAME_2 = "topicQueue2";
        public static final String EXCHANGE_NAME = "topicExchange";
    
        /**
         * 声明交交换器
         *
         * @return {@code TopicExchange }
         */
        @Bean
        public TopicExchange topicExchange() {
            return new TopicExchange(EXCHANGE_NAME);
        }
    
        /**
         * 声明第一个队列
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue topicQueue1() {
            return new Queue(QUEUE_NAME_1);
        }
    
        /**
         * 绑定第一个队列，使用带通配符的路由键 china.#
         *
         * @param topicExchange 话题交流
         * @param topicQueue1   主题队列1
         * @return {@code Binding }
         */
        @Bean
        public Binding topicBinding1(TopicExchange topicExchange, Queue topicQueue1) {
            return BindingBuilder.bind(topicQueue1).to(topicExchange).with("china.#");
        }
    
        /**
         * 声明第二个队列
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue topicQueue2() {
            return new Queue(QUEUE_NAME_2);
        }
    
        /**
         * 绑定第二个队列，使用带通配符的路由键 #.news
         *
         * @param topicExchange 话题交流
         * @param topicQueue2   主题队列2
         * @return {@code Binding }
         */
        @Bean
        public Binding topicBinding2(TopicExchange topicExchange, Queue topicQueue2) {
            return BindingBuilder.bind(topicQueue2).to(topicExchange).with("#.news");
        }
    }
    ```
    
- publisher
    
    使用方法：`convertAndSend(String *exchange*, String *routingKey*, Object *object*)`
    
    ```java
    @SpringBootTest
    public class TopicSendAMQP {
        @Autowired
        private RabbitTemplate rabbitTemplate;
    
        @Test
        public void send(){
            String exchangeName = "topicExchange";
            String[] routingKeys = {"china.news","japan.news"};
            rabbitTemplate.convertAndSend(exchangeName,routingKeys[0],"中国的新闻");
            rabbitTemplate.convertAndSend(exchangeName,routingKeys[1],"日本的新闻");
        }
    }
    ```
    
- subscriber
    
    ```java
    @Component
    public class TopicReceiveAMQP {
    
        @RabbitListener(queues = {TopicsConfig.QUEUE_NAME_1})
        public void receive1(String msg) {
            System.out.println("[✔️]消费者1接收消息：'" + msg + "'");
        }
    
        @RabbitListener(queues = {TopicsConfig.QUEUE_NAME_2})
        public void receive2(String msg) {
            System.out.println("[✔️]消费者2接收消息：'" + msg + "'");
        }
    }
    ```
    

# 配置

## 消息转换器

RabbitMQ 发送的消息是字节数组，而 SpringAMQP 集成的 `RabbitTemplate` 的 `convertAndSend()` 方法发送的是 `Object` 类型数据，所以在这中间，SpringAMQP 会将对象序列化为字节数组然后发送给 RabbitMQ，接收的时候反序列化为对象

SpringAMQP 默认使用 `SimpleMessageConverter` ，只能对字符串、字节数组和实现了 `Serializable` 接口的简单对象的转换。

所以如果发送的消息是自定义对象和其他类型，就需要更换 `MessageConverter`，流行的是`Jackson2JsonMessageConverter` 。

- 引入依赖
    
    ```xml
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
    ```
    
- 配置 Converter
    
    在生产者服务和消费者服务中都需要配置 MessageConverter
    
    ```java
    @Configuration
    public class ConverterConfig {
    
        @Bean
        public MessageConverter messageConverter() {
            return new Jackson2JsonMessageConverter();
        }
    }
    ```
    
    或者
    
    ```java
    @Configuration
    public class ConverterConfig {
    
        @Bean
        public Jackson2JsonMessageConverter jackson2JsonMessageConverter() {
            return new Jackson2JsonMessageConverter();
        }
    
        @Bean
        public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory, Jackson2JsonMessageConverter jackson2JsonMessageConverter) {
            RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
            rabbitTemplate.setMessageConverter(jackson2JsonMessageConverter);
            return rabbitTemplate;
        }
    }
    ```
    

## 消息可靠性

消息在传输过程中可能因为各种各样原因丢失，保证消息传输的可靠性是非常重要的

### 生产者消息确认

生产者消息确认确保消息成功发送到交换器或者队列，并且在成功或失败后调用回调函数

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0536d6a3-c650-4e09-9e75-6d2605917d10/de39512a-f175-4d45-9628-5b0332dd7e76/image.png)

首先需要给每个消息设置一个全局唯一ID

对于生产者发送消息到交换器以及交换器发送消息到队列，这中间可能存在的消息丢失，可以通过两个机制来确认：

- `publisher-confirm`
    
    - 消息成功投递到交换机，返回 ack
    - 消息未投递到交换机，返回 nack
- `publisher-return`
    
    - 消息投递到交换机了，但是没有路由到队列。返回 ACK，及路由失败原因。
- yaml
    
    在生产者中配置
    
    ```yaml
    spring:
      rabbitmq:
        publisher-confirm-type: correlated
        publisher-returns: true
        template:
          mandatory: true
    ```
    
    - `publisher-confirm-type`：开启 publisher-confirm 机制，有两个值：
        - `simple`：同步等待 confirm 结果，直到超时
        - `correlated`：异步回调自定义的 `ConfirmCallback`
    - `publisher-returns`：开启 publisher-return 机制，异步回调自定义的 `ReturnCallback`
    - `template.mandatory`：定义交换器发送消息到队列失败的策略
        - `true`：调用 `RetuenCallback`
        - `false`：直接丢弃消息
- config
    
    每个 RabbitTemplate 只能设置一个 ReturnCallback
    
    每个生产者都可以自定义自己的 ConfirmCallback
    
    这是将 ReturnCallback 和 ConfirmCallback 全部写在配置类中的效果：
    
    （虽然已经在配置文件中设置了 template.mandatory 属性为 true，但是它只对默认的 RabbitTemplate 生效，这里自定义了 RabbitTemplate，所以需要手动再次设置）
    
    ```java
    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        // 设置 ConfirmCallback
        rabbitTemplate.setConfirmCallback(((correlationData, ack, cause) -> {
            if (ack) {
                log.info("消息已发送交换器:{}", correlationData);
            } else {
                log.error("消息未发送交换器:{}", cause);
            }
        }));
        // 设置 ReturnCallBack
        rabbitTemplate.setReturnsCallback((message) -> {
            log.error("消息未发送到队列，message：{},交换机：{},路由键：{},原因：{},应答码：{}",
                    new String(message.getMessage().getBody()),
                    message.getExchange(),
                    message.getRoutingKey(),
                    message.getReplyText(),
                    message.getReplyCode());
        });
        // 开启 ReturnCallback
        rabbitTemplate.setMandatory(true);
        return rabbitTemplate;
    }
    ```
    
    这是通过织入方式设置 ReturnCallBack ，并且在生产者中设置 ConfirmCallback 的效果：
    
    ```java
    @Configuration
    public class CommonConfig implements ApplicationContextAware {
        @Override
        public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
            // 获取RabbitTemplate
            RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
            // 设置ReturnCallback
            rabbitTemplate.setReturnCallback((message) -> {
                // 投递失败，记录日志
                log.error("消息未发送到队列，message：{},交换机：{},路由键：{},原因：{},应答码：{}",
                    new String(message.getMessage().getBody()),
                    message.getExchange(),
                    message.getRoutingKey(),
                    message.getReplyText(),
                    message.getReplyCode());
                // 如果有业务需要，可以重发消息
            });
        }
    }
    ```
    
    ```java
    @Test
    public void sendObjectAndConfirm(){
    		// 交换器
        String exchangeName = "fanoutExchange";
        // 消息
        User user = new User(110L, "admin", "admin");
        // 设置消息唯一ID
        CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
        correlationData.getFuture().addCallback(
                result -> {
                    if (result.isAck()){
                        log.info("消息发送发送交换器成功！ID：{}",correlationData.getId());
                    }else {
                        log.error("消息发送交换器失败！ID：{}，原因：{}",correlationData.getId(),result.getReason());
                    }
                },
                ex -> {
                    log.error("消息发送交换器异常！ID：{}，原因：{}",correlationData.getId(),ex.getMessage());
                }
        );
        rabbitTemplate.convertAndSend(exchangeName,"fff",user,correlationData);
    }
    ```
    
- publisher
    
    发送消息时，建议使用 `CorrelationData` 对象设置一个唯一ID，便于监控和排查
    
    ```java
    @Test
    public void send2() {
        String exchangeName = "directExchange";
        String[] routingKeys = {"blue","yellow","green"};
        rabbitTemplate.convertAndSend(exchangeName,"routingKeys","blue",new CorrelationData(UUID.randomUUID().toString()));
        rabbitTemplate.convertAndSend(exchangeName,"routingKeys","yellow",new CorrelationData(UUID.randomUUID().toString()));
        rabbitTemplate.convertAndSend(exchangeName,"routingKeys","green",new CorrelationData(UUID.randomUUID().toString()));
    }
    ```
    

### 消息持久化

消息的持久化是防止 RabbitMQ Server 因为宕机而导致的消息丢失

有三种持久化手段：

- 交换器持久化
    
    交换器的元数据被持久化到磁盘
    
    声明交换器时，开启持久化选项：
    
    ```java
    // 交换器名称、是否持久化、无队列绑定是否自动删除
    FanoutExchange(String name, boolean durable, boolean autoDelete)
    ```
    
- 队列持久化
    
    队列的元数据被持久化到磁盘
    
    声明队列时，开启持久化选项：
    
    ```java
    // 队列名称、是否持久化、当前连接是否独占队列、消费者断开后是否自动删除队列
    Queue(String name, boolean durable, boolean exclusive, boolean autoDelete)
    ```
    
- 消息持久化
    
    消息的持久化必须在持久化队列和持久化交换器中才能生效！
    
    发送消息时，设置消息的 MessageProperties 来开启消息持久化
    
    ```java
    MessageProperties messageProperties = new MessageProperties();
    messageProperties.setDeliveryMode(MessageDeliveryMode.PERSISTENT); // 设置消息为持久化
    Message message = new Message("Hello, Persistent World!".getBytes(), messageProperties);
    ```
    
    或者在发送消息时，通过 MessageBuild 来构建消息
    
    ```java
    Message message = MessageBuilder
            .withBody(objectMapper.writeValueAsBytes(user))
            .setDeliveryMode(MessageDeliveryMode.PERSISTENT) // 设置消息为持久化
            .build();
    ```
    

RabbitMQ 原生默认消息非持久化，SpringAMQP 默认消息持久化！

### 消费者消息确认

该机制确保消息被消费者消费，不会因为消费者服务宕机而导致消息丢失，也确保消息不重复消费

SpringAMQP 有三种确认机制，通过 `AcknowledgeMode` 配置：

- `AUTO`（自动确认）：默认模式，消费者逻辑完成，没有异常，消息被自动确认
    
- `MANUAL`（手动确认）：需要手动调用 `Channel.basicAck` 或 `Channel.basicNack`
    
- `NONE`（不确认）：消息被投递给消费者后立即确认，不管消息处理成不成功
    
- yaml
    
    在消费者服务中配置
    
    ```yaml
    spring:
      rabbitmq:
        listener:
          simple:
            acknowledge-mode: manual # 设置为手动确认
    ```
    
- 手动确认的情况
    
    ```java
    @RabbitListener(queues = "myQueue", ackMode = "MANUAL")
    public void manualAckListener(String message, Channel channel, @Header(AmqpHeaders.DELIVERY_TAG) long deliveryTag) {
        try {
            System.out.println("Received message: " + message);
            // 处理消息逻辑
            channel.basicAck(deliveryTag, false);
        } catch (Exception e) {
            // 处理失败时，选择重新投递或丢弃
            try {
                channel.basicNack(deliveryTag, false, true); // 重新投递
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }
    ```
    
    - `basicAck()` 表示确认消息，方法参数：
        - `deliveryTag`：消费者通道中唯一递增的消息标识符，代表消息在通道中的位置
        - `multiple`：是否确认所有小于等于 `deliveryTag` 的消息（包括之前未确认的消息）
    - `basicNack()` 表示拒绝消息，方法参数：
        - `deliveryTag`：同上，标识要拒绝的消息
        - `multiple`：同上，批量拒绝
        - `requeue`：是否重新入队，`true` 表示重入队尾，`false` 表示丢弃或进入死信队列
    - `basicReject()`：类似于 `basicNack()`
        - `deliveryTag`：同上
        - `requeue`：同上

### 消费失败重试

该机制是为了应对消费者服务长时间异常，导致消息无法消费，让消息重新入队，造成的死循环甚至超载

为了不让消费者异常后消息重新入队，可以在消费者服务中，使用 Spring 的 RetryInterceptor 对信息进行重试

- yaml
    
    ```yaml
    spring:
      rabbitmq:
        listener:
          simple:
            retry:
              enabled: true # 开启消费者失败重试
              initial-interval: 1000 # 初始的失败等待时长为1秒
              multiplier: 1 # 失败的等待时长倍数，下次等待时长 = multiplier * last-interval
              max-attempts: 3 # 最大重试次数
              stateless: true # true无状态；false有状态。如果业务中包含事务，这里改为false
    ```
    

在此配置下，重试达到上限后消息会被删除，这种情况下消息就丢失了（进入死信交换器）

为了在重试机制下，不让消息丢失，需要自定义失败策略：

- `RejectAndDontRequeueRecoverer`：默认，重试上限后，消费者发送 reject，并且消息不重入队
- `ImmediateRequeueMessageRecoverer`：重试上限后，消费者发送 nack，重新入队
- `RepublishMessageRecoverer`：重试上限后，将消息发送到指定交换器

如果需要失败消息在转发到死信交换器之前执行业务，可以配置 `RepublishMessageRecoverer`

- config
    
    ```java
    @Configuration
    public class ErrorMessageConfig {
    
        /**
         * 错误消息交换器
         *
         * @return {@code DirectExchange }
         */
        @Bean
        public DirectExchange errorMessageExchange() {
            return new DirectExchange("error.direct");
        }
    
        /**
         * 错误消息队列
         *
         * @return {@code Queue }
         */
        @Bean
        public Queue errorMessageQueue() {
            return new Queue("error.queue");
        }
    
        /**
         * 错误消息绑定，使用路由键 error
         *
         * @param errorMessageExchange 错误消息交换
         * @param errorMessageQueue    错误消息队列
         * @return {@code Binding }
         */
        @Bean
        public Binding errorMessageBinding(DirectExchange errorMessageExchange, Queue errorMessageQueue) {
            return BindingBuilder.bind(errorMessageQueue).to(errorMessageExchange).with("error");
        }
    
        /**
         * 设置 MessageRecoverer 为 RepublishMessageRecoverer
         *
         * @param rabbitTemplate rabbit模板
         * @return {@code MessageRecoverer }
         */
        @Bean
        public MessageRecoverer rePublishMessageConverter(RabbitTemplate rabbitTemplate) {
            return new RepublishMessageRecoverer(rabbitTemplate,"error.direct","error");
        }
    }
    ```
    

需要注意的是，在此种情况下，一旦达到重试上限，`RepublishMessageRecoverer` 将被立即调用，消息不会进入死信交换器

## 死信交换器

## 惰性队列

# 集群