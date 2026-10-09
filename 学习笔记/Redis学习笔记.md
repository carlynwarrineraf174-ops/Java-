# Redis 学习笔记（黑马程序员 Redis 入门到实战）

> 课程来源：[黑马程序员 Redis 入门到实战教程（BV1cr4y1671t）](https://www.bilibili.com/video/BV1cr4y1671t/)
> 共 175 集，分四大篇章：**基础篇（22）+ 实战篇（72）+ 高级篇（49）+ 原理篇（31）**
> 配套项目：**黑马点评**（`D:\java\Redis\hm-dianping`）
>
> 本篇标记说明：
> - 🎯 = 面试高频
> - ⚠️ = 易错点 / 踩坑记录
> - 💻 = 本项目已实现，附真实代码
> - 🧠 = 记忆口诀

---

# 目录

- [基础篇](#基础篇)
- [实战篇](#实战篇)
- [高级篇](#高级篇)
- [原理篇](#原理篇)
- [面试题汇总](#面试题汇总)
- [知识图谱](#知识图谱)

---

# 基础篇

## 基础篇-01 · Redis 入门课程介绍

**学习路线：** 基础篇（命令 + Java 客户端）→ 实战篇（黑马点评项目）→ 高级篇（分布式缓存 + 多级缓存 + 最佳实践）→ 原理篇（底层数据结构 + 网络模型 + 内存回收）

---

## 基础篇-02 · 认识 NoSQL

### 1. SQL vs NoSQL

| | SQL（关系型） | NoSQL（非关系型） |
|---|---|---|
| 数据结构 | 结构化（表、行、列） | 非结构化（键值、文档、图） |
| 数据关联 | 关联查询（JOIN） | 无关联 |
| 查询方式 | SQL | 各自的 API |
| 事务 | ACID | BASE（基本可用、软状态、最终一致） |
| 存储方式 | 磁盘 | 内存为主（Redis）/ 磁盘（MongoDB） |
| 扩展方式 | 垂直扩展（加硬件） | **水平扩展（加节点）** |

### 2. NoSQL 的四大分类

| 类型 | 代表 | 特点 |
|---|---|---|
| **键值型** | **Redis** | key-value，查询快 |
| 文档型 | MongoDB | 存 JSON 文档 |
| 列族型 | HBase | 海量数据 |
| 图形型 | Neo4j | 社交关系 |

**🧠 记忆：** NoSQL = **N**ot **o**nly **SQL**（不仅仅是 SQL）

---

## 基础篇-03 · 认识 Redis

### 1. Redis 是什么

**概念：** Redis（**RE**mote **DI**ctionary **S**erver，远程字典服务器）是一个基于**内存**的 **key-value** 结构数据库，用 **C 语言**编写，支持持久化。

**本质：** 把数据放在内存里的"大 Map"，读写都是 O(1) 级别。

### 2. Redis 的八大特点 🎯

| 特点 | 说明 |
|---|---|
| **内存存储** | 读写速度极快（10w+ QPS） |
| **key-value 结构** | value 支持多种数据类型 |
| **单线程** | 命令串行执行，天然线程安全（6.0 后网络 IO 多线程） |
| **支持持久化** | RDB + AOF，重启不丢数据 |
| **支持集群** | 主从、哨兵、分片集群 |
| **支持事务** | 有限的事务（MULTI/EXEC） |
| **支持 Lua 脚本** | 保证多条命令的**原子性** 🎯 |
| **支持发布订阅** | Pub/Sub 消息通信 |

### 3. Redis vs Memcached

| | Redis | Memcached |
|---|---|---|
| 数据类型 | **丰富**（String/Hash/List/Set/ZSet...） | 只有 String |
| 持久化 | **支持** | 不支持 |
| 集群 | **支持** | 不支持 |
| 单线程 | 是 | 多线程 |

---

## 基础篇-04 · 安装 Redis 及启动的三种方式

### 三种启动方式

| 方式 | 命令 | 特点 |
|---|---|---|
| 默认启动 | `redis-server` | 前台运行，关掉窗口就停 |
| 指定配置 | `redis-server redis.conf` | 按配置文件启动（**推荐**） |
| 开机自启 | 配置 systemd | 后台守护进程 |

### 关键配置项

```conf
bind 127.0.0.1 -::1        # 绑定地址，改成 0.0.0.0 才能外部访问
protected-mode yes          # 保护模式
port 6379                   # 端口
daemonize no                # 是否后台运行
requirepass 123456          # 密码（默认无密码）
dir ./                      # 持久化文件目录
```

**⚠️ 踩坑记录（本项目真实遇到）：**
```conf
bind 127.0.0.1 -::1     # 只监听回环地址
```
如果 Spring Boot 配置里写 `host: 192.168.43.12`（本机局域网 IP），会报：
```
Connection refused
```
**原因**：Redis 只绑定了 `127.0.0.1` 和 `::1`，压根没监听那个局域网 IP。
**解决**：要么把配置改成 `localhost`，要么把 Redis 的 bind 改成 `0.0.0.0`。

---

## 基础篇-05 · Redis 命令行客户端

```bash
redis-cli                          # 连接本机
redis-cli -h 127.0.0.1 -p 6379     # 指定主机端口
redis-cli -a 123456                # 带密码
redis-cli --scan --pattern 'user:*'  # 模糊查询 key
redis-cli --eval script.lua key1 , arg1 arg2   # 执行 Lua 脚本
redis-cli shutdown                 # 关闭
```

**🧠 记忆：** `--eval` 的逗号前是 **KEYS**，逗号后是 **ARGV**

---

## 基础篇-06 · 图形化界面客户端

常见工具：**RedisInsight**（官方）、**Another Redis Desktop Manager**、**Redis Desktop Manager**

---

## 基础篇-07 · Redis 数据结构介绍 🎯

### 五种基本数据类型

| 类型 | 结构 | 典型场景 |
|---|---|---|
| **String** | 字符串 | 缓存对象、计数器、分布式锁 |
| **Hash** | 哈希表 | 存储对象字段（用户信息） |
| **List** | 双向链表 | 消息队列、栈 |
| **Set** | 无序集合 | 去重、共同好友、抽奖 |
| **ZSet** | 有序集合 | 排行榜、Feed 流 |

### 五种特殊类型（后续课程才讲）

| 类型 | 用途 |
|---|---|
| **GEO** | 地理位置计算（附近商铺） |
| **BitMap** | 位存储（签到） |
| **HyperLogLog** | 基数统计（UV 统计） |
| **Stream** | 消息队列 |

---

## 基础篇-08 · 通用命令

```bash
KEYS pattern        # 查所有符合的 key（⚠️ 生产禁用，会阻塞）
DEL key             # 删除
EXISTS key          # 判断存在
EXPIRE key 10       # 设置过期时间（秒）
TTL key             # 查剩余时间（-1=永不过期，-2=已删除）
TYPE key            # 查类型
SELECT 0            # 切换库（默认 16 个库，0-15）
```

### ⚠️ KEYS 为什么不能在生产用

`KEYS *` 会**遍历所有 key**，Redis 是单线程，执行期间**会阻塞其他所有命令**。
**替代方案：** `SCAN cursor MATCH pattern COUNT 10` 分批游标遍历，不阻塞。

**🧠 记忆：** TTL 返回 **-1 永不过期**、**-2 已经没了**

---

## 基础篇-09 · String 类型

```bash
SET name 张三
GET name
SETEX name 10 张三        # 设置同时带过期时间（秒）
SETNX name 张三           # 不存在才设置（分布式锁的基础）
INCR count                # 自增（原子）
INCRBY count 5            # 增加指定值
DECR count                # 自减
MSET k1 v1 k2 v2          # 批量设置
MGET k1 k2                # 批量获取
```

### 🎯 面试点：INCR 是原子的

Redis 的 `INCR` 命令是**原子操作**，因为 Redis 单线程执行命令。
**应用：** 计数器（阅读量）、分布式 ID（`INCR` 生成序列号）、限流。

### ⚠️ SETNX + EXPIRE 不是原子的

```bash
SETNX lock 1     # ① 拿到锁
EXPIRE lock 10   # ② 设置过期时间
# ⚠️ 如果 ① 之后服务宕机，② 没执行 → 死锁！
```
**解决**：用一条命令 `SET lock 1 EX 10 NX`（原子）

---

## 基础篇-10 · Key 的层级格式

**推荐写法：** 用冒号分隔，形成层级
```
项目名:业务名:类型:id
hmdp:user:1
cache:shop:1
login:token:uuid
```

**好处：**
- 可读性强
- 图形化客户端会自动按 `:` 分层展示
- 便于用 `SCAN pattern` 检索

**💻 本项目的 key 设计（`RedisConstants.java`）：**
```java
LOGIN_CODE_KEY       = "login:code:";       // 验证码
LOGIN_USER_KEY       = "login:token:";      // 登录会话
CACHE_SHOP_KEY       = "cache:shop:";       // 商铺缓存
CACHE_SHOP_TYPE_KEY  = "cache:shopType:";   // 商铺类型缓存
LOCK_SHOP_KEY        = "lock:shop:";        // 缓存重建锁
SECKILL_STOCK_KEY    = "seckill:stock:";    // 秒杀库存
BLOG_LIKED_KEY       = "blog:liked:";       // 博客点赞
FOLLOW_KEY           = "follow:";           // 关注
FEED_KEY             = "feed:";             // Feed 流收件箱
SHOP_GEO_KEY         = "shop:geo:";         // 商铺地理位置
USER_SIGN_KEY        = "sign:";             // 用户签到
```

---

## 基础篇-11 · Hash 类型

```bash
HSET user:1 name 张三 age 20    # 设置字段
HGET user:1 name                # 获取单个字段
HMGET user:1 name age           # 批量获取
HGETALL user:1                  # 获取所有字段
HDEL user:1 name                # 删除字段
HKEYS user:1                    # 所有字段名
HLEN user:1                     # 字段数量
HINCRBY user:1 age 1            # 字段自增
```

### String vs Hash 存对象

| | String（存 JSON） | Hash |
|---|---|---|
| 存储 | `SET user:1 '{"name":"张三"}'` | `HSET user:1 name 张三 age 20` |
| 改单个字段 | ❌ 要读出整个 JSON 反序列化再写回 | ✅ `HSET user:1 age 21` |
| 内存占用 | 较小（数据紧凑） | 较大（每个字段有额外开销） |

**选型：**
- 对象**整体读写** → String
- 需要**频繁改单个字段** → Hash

**💻 本项目用 Hash 存登录会话**（`UserServiceImpl.login`）：
```java
Map<String, Object> userMap = BeanUtil.beanToMap(user, new HashMap<>(),
        new CopyOptions().setIgnoreNullValue(true)
                .setFieldValueEditor((fieldName, fieldValue) ->
                        fieldValue == null ? null : fieldValue.toString()));
stringRedisTemplate.opsForHash().putAll(LOGIN_USER_KEY + token, userMap);
stringRedisTemplate.expire(LOGIN_USER_KEY + token, LOGIN_USER_TTL, TimeUnit.SECONDS);
```

**⚠️ 踩坑记录（本项目真实遇到两次）：**

**坑 1：`setFieldValueEditor` 无条件 `.toString()` 会 NPE**
```
NullPointerException: Cannot invoke "Object.toString()" because "fieldValue" is null
    at UserServiceImpl.lambda$login$0(UserServiceImpl.java:76)
    at cn.hutool.core.bean.copier.CopyOptions.editFieldValue
```
**原因**：新建用户的 `password`、`icon` 是 `null`。
**关键**：`setIgnoreNullValue(true)` **救不了**——Hutool 是**先执行 editor、后判断 null** 的。
**解决**：editor 里加 null 保护 → `fieldValue == null ? null : fieldValue.toString()`

**坑 2：`stringRedisTemplate.expire()` 抛 StackOverflowError**
```
java.lang.StackOverflowError
    at org.springframework.data.redis.connection.DefaultedRedisConnection.pExpire(...)
    at org.springframework.data.redis.connection.DefaultedRedisConnection.pExpire(...)
    ...（无限递归）
```
**原因**：`redisson-spring-boot-starter:3.27.2` 带入了 `redisson-spring-data-32`（按 Spring Data Redis **3.2** 实现），
而项目用的是 Spring Data Redis **3.5.13**，`pExpire` 方法没被覆盖到，落到默认实现（**默认实现就是调自己**）→ 无限递归。
**解决**：把 `redisson-spring-boot-starter` 换成 `redisson` 核心包（只用 `RedissonClient`，不需要它接管连接工厂）。

---

## 基础篇-12 · List 类型

```bash
LPUSH list a b c      # 左侧插入（栈：后进先出）
RPUSH list a b c      # 右侧插入（队列：先进先出）
LPOP list             # 左侧弹出
RPOP list             # 右侧弹出
LRANGE list 0 -1      # 范围查询（-1 表示最后一个）
BLPOP list 5          # 阻塞式弹出，最多等 5 秒
LLEN list             # 长度
```

### 用 List 模拟数据结构

| 结构 | 命令组合 |
|---|---|
| **栈**（LIFO） | `LPUSH` + `LPOP`（同一侧进出） |
| **队列**（FIFO） | `LPUSH` + `RPOP`（异侧进出） |
| **阻塞队列** | `LPUSH` + `BRPOP` |

**🧠 记忆：** **同侧进出是栈，异侧进出是队列**

---

## 基础篇-13 · Set 类型

```bash
SADD set a b c            # 添加
SREM set a                # 删除
SCARD set                 # 元素个数
SISMEMBER set a           # 判断是否存在
SMEMBERS set              # 所有元素
SINTER set1 set2          # 交集（共同好友）
SUNION set1 set2          # 并集
SDIFF set1 set2           # 差集
```

### 🎯 应用场景

| 场景 | 实现 |
|---|---|
| **去重** | `SADD` 天然去重 |
| **共同好友** | `SINTER` 求交集 |
| **抽奖** | `SPOP` 随机弹出 |
| **一人一单** | `SISMEMBER` 判断是否已购 |

**💻 本项目 `seckill.lua` 用 Set 实现一人一单：**
```lua
local orderKey = "seckill:order:"..voucherId
if(redis.call('sismember',orderKey,userId) == 1) then
    return 2           -- 已购买过
end
redis.call('sadd',orderKey,userId)
```

---

## 基础篇-14 · SortedSet（ZSet）类型

```bash
ZADD zset 10 张三 20 李四      # 添加（score member）
ZRANGE zset 0 -1               # 按分数升序
ZREVRANGE zset 0 -1            # 按分数降序
ZRANGEBYSCORE zset 0 15        # 按分数范围查
ZSCORE zset 张三               # 查某成员分数
ZINCRBY zset 5 张三            # 增加分数
ZRANK zset 张三                # 升序排名
ZREVRANK zset 张三             # 降序排名
ZCOUNT zset 0 100              # 分数区间元素个数
```

### 🎯 应用场景

| 场景 | 实现 |
|---|---|
| **排行榜** | `ZADD` + `ZREVRANGE` |
| **点赞排序** | score = 时间戳，按时间排序 |
| **Feed 流滚动分页** | `ZREVRANGEBYSCORE` + `LIMIT` |
| **附近的人** | score = GEO 哈希值 |

### ⚠️ 面试点：ZSet 底层为什么用跳表而不是红黑树？

| | 跳表（SkipList） | 红黑树 |
|---|---|---|
| 实现复杂度 | **简单** | 复杂 |
| 范围查询 | **容易**（链表天然有序） | 需要中序遍历 |
| 并发 | 容易做无锁 | 难 |
| 平均复杂度 | O(logN) | O(logN) |

---

## 基础篇-15 · Java 客户端对比

| 客户端 | 特点 |
|---|---|
| **Jedis** | 直连，**线程不安全**，需要连接池 |
| **Lettuce** | 基于 Netty，**线程安全**，支持异步/响应式（Spring Boot 2.x 后默认） |
| **Redisson** | 分布式对象、分布式锁、各种分布式集合 |

**🧠 记忆：** **Jedis 要配连接池，Lettuce 天然安全，Redisson 专做分布式**

---

## 基础篇-16 · Jedis 快速入门

```java
// 1. 引入依赖
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>

// 2. 建立连接
private Jedis jedis;
@BeforeEach
void setUp() {
    jedis = new Jedis("127.0.0.1", 6379);
    jedis.auth("123456");
    jedis.select(0);
}

// 3. 使用
@Test
void testString() {
    jedis.set("name", "张三");
    String name = jedis.get("name");
}
```

---

## 基础篇-17 · Jedis 连接池

**为什么需要连接池：** Jedis 是**线程不安全**的，多线程共用会出错；频繁创建销毁连接开销大。

```java
private static final JedisPool jedisPool;

static {
    JedisPoolConfig config = new JedisPoolConfig();
    config.setMaxTotal(8);        // 最大连接
    config.setMaxIdle(8);         // 最大空闲
    config.setMinIdle(0);         // 最小空闲
    config.setMaxWait(Duration.ofMillis(1000));  // 最大等待时间
    jedisPool = new JedisPool(config, "127.0.0.1", 6379, 1000, "123456");
}

public static Jedis getJedis() {
    return jedisPool.getResource();
}
```

---

## 基础篇-18 · 认识 SpringDataRedis

**Spring Data Redis 做了什么：**
- 统一封装了 Jedis、Lettuce 等客户端
- 提供 `RedisTemplate` 统一 API
- 支持 Spring 的序列化机制

```
Spring Data Redis
    ├── Lettuce（默认，线程安全）
    └── Jedis（需手动引入）
```

---

## 基础篇-19 · RedisTemplate 快速入门

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
      # password: 123456
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0
```

```java
@Autowired
private RedisTemplate redisTemplate;

@Test
void testString() {
    redisTemplate.opsForValue().set("name", "张三");
    Object name = redisTemplate.opsForValue().get("name");
}
```

### API 与数据类型的对应

| Redis 类型 | RedisTemplate 方法 |
|---|---|
| String | `opsForValue()` |
| Hash | `opsForHash()` |
| List | `opsForList()` |
| Set | `opsForSet()` |
| ZSet | `opsForZSet()` |
| GEO | `opsForGeo()` |
| HyperLogLog | `opsForHyperLogLog()` |
| Stream | `opsForStream()` |

---

## 基础篇-20 · RedisSerializer 序列化器 🎯

### ⚠️ 默认序列化器的问题

`RedisTemplate` 默认用 **JDK 序列化**，存进去的数据**不可读**：
```
\xac\xed\x00\x05t\x00\x04name     ← 一堆乱码
```

### 四种序列化器对比

| 序列化器 | 特点 | 可读性 |
|---|---|---|
| **JDK**（默认） | Java 原生 | ❌ 乱码 |
| **String** | 字符串 | ✅ 可读 |
| **JSON** | 通用，跨语言 | ✅ 可读，体积略大 |
| **Hash** | 内存占用小 | ❌ 不可读 |

### 自定义 RedisTemplate

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);

        // 通用序列化器
        GenericJackson2JsonRedisSerializer jsonSerializer = new GenericJackson2JsonRedisSerializer();
        // key 用 String
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        // value 用 JSON
        template.setValueSerializer(jsonSerializer);
        template.setHashValueSerializer(jsonSerializer);

        template.afterPropertiesSet();
        return template;
    }
}
```

**⚠️ JSON 序列化器的缺点：** 为了反序列化时知道类型，会**额外存类名**：
```json
{"@class":"com.hmdp.entity.User","name":"张三","age":20}
```
→ **多占内存**，且有安全隐患。

---

## 基础篇-21 · StringRedisTemplate 🎯

**为了解决 JSON 序列化的两个问题：**
1. 额外存 `@class` 浪费内存
2. 有反序列化安全风险（`@class` 可控 → 反序列化漏洞）

**做法：** key 和 value **统一用 String 序列化器**，由**我们自己**手动完成对象 ↔ JSON 的转换。

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

// 手动序列化
@Test
void testSaveUser() throws JsonProcessingException {
    User user = new User("张三", 20);
    String json = mapper.writeValueAsString(user);      // 手动转 JSON
    stringRedisTemplate.opsForValue().set("user:1", json);

    // 手动反序列化
    String json2 = stringRedisTemplate.opsForValue().get("user:1");
    User user2 = mapper.readValue(json2, User.class);
}
```

**💻 本项目全程用 `StringRedisTemplate`：**
```java
stringRedisTemplate.opsForValue().get(CACHE_SHOP_KEY + id)
stringRedisTemplate.opsForHash().putAll(LOGIN_USER_KEY + token, userMap)
stringRedisTemplate.expire(LOGIN_USER_KEY + token, LOGIN_USER_TTL, TimeUnit.SECONDS)
```

**🧠 记忆：** **要可读、要跨语言 → String 序列化器；对象自己手动转 JSON**

---

## 基础篇-22 · RedisTemplate 操作 Hash 类型

```java
// 存
stringRedisTemplate.opsForHash().put("user:1", "name", "张三");
stringRedisTemplate.opsForHash().putAll("user:1", map);

// 取
Object name = stringRedisTemplate.opsForHash().get("user:1", "name");
Map<Object, Object> entries = stringRedisTemplate.opsForHash().entries("user:1");

// 删
stringRedisTemplate.opsForHash().delete("user:1", "name");

// 判断字段是否存在
Boolean has = stringRedisTemplate.opsForHash().hasKey("user:1", "name");
```

**💻 本项目拦截器里读会话：**
```java
Map<Object, Object> userMap = stringRedisTemplate.opsForHash()
        .entries(RedisConstants.LOGIN_USER_KEY + token);
// 转成对象
UserDTO userDto = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
UserHolder.saveUser(userDto);
```

---

# 实战篇

## 实战篇-01 · Redis 企业实战课程介绍

**项目：黑马点评**（类似大众点评）
技术栈：Spring Boot + MyBatis-Plus + MySQL + Redis + Nginx

---

## 实战篇-02 ~ 07 · 短信登录（基于 Session）

### 1. 基于 Session 的登录流程

```
① 发送验证码
   前端提交手机号 → 后端生成验证码 → 保存到 Session → 发送短信

② 登录/注册
   前端提交手机号+验证码 → 校验 Session 中的验证码
   → 按手机号查用户，不存在则创建 → 保存用户到 Session

③ 校验登录状态
   拦截器从 Session 取用户，存在则放行，不存在返回 401
```

### 2. 拦截器实现

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request,
                             HttpServletResponse response, Object handler) {
        if (UserHolder.getUser() == null) {
            response.setStatus(401);
            return false;
        }
        return true;
    }

    @Override
    public void afterCompletion(...) {
        UserHolder.removeUser();   // ⚠️ 必须清理，否则内存泄漏
    }
}
```

**⚠️ 关键：`afterCompletion` 必须清理 ThreadLocal**，否则线程池复用时会串数据。

### 3. 隐藏用户敏感信息

Session 里存 `User` 会包含 `password`，所以：
- **存** → 转成 `UserDTO`（只有 id/nickName/icon）
- **取** → `BeanUtil.copyProperties(user, UserDTO.class)`

**💻 本项目用 `UserDTO`**（`id` / `nickName` / `icon` 三个字段）：
```java
public class UserDTO {
    private Long id;
    private String nickName;
    private String icon;
}
```

### 4. ⚠️ Session 共享问题

**问题：** 多台 Tomcat 之间 Session 不共享 → 用户在第 1 台登录，请求打到第 2 台就未登录。

**解决方案对比：**

| 方案 | 优点 | 缺点 |
|---|---|---|
| Session 复制 | 简单 | 每台都存全量，浪费内存 |
| Session 粘滞 | 简单 | 单点故障，负载不均 |
| **Redis 共享 Session** | **无状态、可水平扩展** | 需要额外组件 |

**🎯 面试点：为什么用 Redis 替代 Session？**
> 1. **服务端无状态**：不依赖单机内存，任意一台服务器都能处理请求
> 2. **水平扩展**：加机器即可，无需 Session 同步
> 3. **统一存储**：多个服务共用一份登录态
> 4. **可设过期时间**：自动清理，不占内存

---

## 实战篇-08 ~ 11 · 基于 Redis 实现短信登录 💻

### 1. 业务流程

```
① 发送验证码
   Redis: SET login:code:{phone} {code} EX 120

② 登录
   校验 Redis 中的验证码 → 查/建用户
   → 生成随机 token（UUID）
   → Redis Hash 存用户信息: HSET login:token:{token} id xx nickName xx
   → 设置过期时间 EX 3600
   → 返回 token 给前端
   
③ 校验
   前端请求头带 authorization: {token}
   → 拦截器从 Redis 查 login:token:{token}
   → 查到就放入 ThreadLocal (UserHolder)
```

### 2. 💻 本项目实现（`UserServiceImpl.login`）

```java
String token = UUID.randomUUID().toString();
Map<String, Object> userMap = BeanUtil.beanToMap(user, new HashMap<>(),
        new CopyOptions().setIgnoreNullValue(true)
                .setFieldValueEditor((f, v) -> v == null ? null : v.toString()));

stringRedisTemplate.opsForHash().putAll(LOGIN_USER_KEY + token, userMap);
stringRedisTemplate.opsForValue().set(LOGOUT_USER_KEY + user.getId(), token);
stringRedisTemplate.expire(LOGIN_USER_KEY + token, LOGIN_USER_TTL, TimeUnit.SECONDS);
return Result.ok(token);
```

### 3. 💻 两级拦截器设计

**本项目用了两个拦截器**（`MvcConfig`）：

```java
registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate))
        .addPathPatterns("/**").order(0);      // ① 所有请求都走：刷新 token
registry.addInterceptor(new LoginInterceptor())
        .excludePathPatterns("/shop/**", "/voucher/**", "/shop-type/**",
                             "/user/code", "/user/login")
        .order(1);                              // ② 需要登录的才校验
```

| 拦截器 | 职责 | 关键点 |
|---|---|---|
| `RefreshTokenInterceptor` | 从 Redis 查 token → 存 UserHolder → **续期** | 全部放行，不拦截 |
| `LoginInterceptor` | 检查 UserHolder 是否为空 | 空则返回 401 |

**为什么要两个？** 🎯
> 因为要**在放行名单之外也刷新 token 有效期**。
> 如果把查询和校验写在一个拦截器里，放行名单里的请求就不会续期了。

**💻 `RefreshTokenInterceptor` 核心逻辑：**
```java
String token = request.getHeader("authorization");
if (StrUtil.isBlank(token)) {
    return true;                    // 没 token 也放行，交给下一个拦截器判断
}
Map<Object, Object> userMap = stringRedisTemplate.opsForHash()
        .entries(RedisConstants.LOGIN_USER_KEY + token);
if (userMap.isEmpty()) {
    return true;
}
UserDTO userDto = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
UserHolder.saveUser(userDto);
// 刷新有效期
stringRedisTemplate.expire(LOGIN_USER_KEY + token, LOGIN_USER_TTL, TimeUnit.SECONDS);
return true;
```

### 4. ⚠️ 踩坑记录：`UserHolder` 两个 ThreadLocal 搞混

**项目里的真实 bug：** `UserHolder` 曾经有两个 ThreadLocal：

```java
private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();   // 一对
private static final ThreadLocal<User>   t2 = new ThreadLocal<>();    // 另一对

public static void saveUser(UserDTO user){ tl.set(user); }   // 写 tl
public static UserDTO getUser(){ return tl.get(); }          // 读 tl
public static void save(User user){ t2.set(user); }          // 写 t2
public static User get(){ return t2.get(); }                 // 读 t2
```

**症状：** 登录校验能通过（`LoginInterceptor` 读 `t2`），但业务代码 `UserHolder.getUser()` 读 `tl` → **永远 null** → NPE。

| 位置 | 调用 | 操作 |
|---|---|---|
| `RefreshTokenInterceptor` | `save(user)` | 写 **t2** |
| `LoginInterceptor` | `get()` | 读 **t2** ← 能通过 |
| `VoucherOrderServiceImpl` | `getUser()` | 读 **tl** ← **永远 null** |

**解决：** 统一成**一个 ThreadLocal + UserDTO**

```java
public class UserHolder {
    private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();
    public static void saveUser(UserDTO user) { tl.set(user); }
    public static UserDTO getUser() { return tl.get(); }
    public static void removeUser() { tl.remove(); }
}
```

**🧠 教训：** **一个 ThreadLocal 只存一种类型，存和取必须严格对应。多个 ThreadLocal 极易搞混。**

### 5. ⚠️ 踩坑记录：放行路径少了 `/`

```java
.excludePathPatterns(
        "/shop/**",
        "upload/**",      // ← 少了开头的 /
        "blog/hot",       // ← 少了开头的 /
        "/user/code",
        "/user/login"
)
```

**症状：** `/blog/hot` 明明在放行名单里，却返回 **401**。
**原因：** Spring Boot 3 的 `PathPatternParser` 要求路径以 `/` 开头。
**解决：** 改成 `"/upload/**"`、`"/blog/hot"`。

### 6. ⚠️ 踩坑记录：错误被伪装成 401

**症状：** 后端抛异常（真实是 500），但前端收到的是 **401 且响应体为空**，完全看不到错误原因。

**原因链：**
```
业务抛异常 → Spring 返回 500
          → 转发到 /error 重新派发
          → /error 不在放行名单里
          → LoginInterceptor 拦截 → setStatus(401) + 清空 body
```

**解决：** 把 `/error` 也加入放行名单
```java
.excludePathPatterns("/shop/**", ..., "/error")
```

**🧠 教训：** 排查 401 时，先确认是"真的未登录"还是"错误被拦截器改写了状态码"。

---

## 实战篇 · 商户查询缓存-01 ~ 13

### 1. 什么是缓存

**缓存 = 数据交换的缓冲区**，把**高频访问**的数据放在**读写快**的介质里（内存），减少数据库压力。

**缓存的作用：**
- 降低后端负载
- 提高读写效率，降低响应时间

**缓存的成本：**
- 数据一致性成本（缓存与数据库不同步）
- 代码维护成本
- 运维成本（集群、预热、雪崩）

### 2. 缓存更新策略 🎯

| 策略 | 说明 | 一致性 | 维护成本 |
|---|---|---|---|
| **内存淘汰** | 不用自己维护，Redis 自动淘汰 | 差 | 无 |
| **超时剔除** | 设置 TTL，过期自动删除 | 一般 | 低 |
| **主动更新** | 修改数据库时**主动更新缓存** | 好 | 高 |

### 3. 主动更新的三种方案

| 方案 | 问题 |
|---|---|
| **先更新缓存，再更新数据库** | ❌ 并发下容易脏数据 |
| **先删除缓存，再更新数据库** | ❌ 并发下仍可能不一致 |
| **先更新数据库，再删除缓存**（**Cache Aside**） | ✅ **推荐** |

**🎯 为什么是"删除缓存"而不是"更新缓存"？**
> 1. **删除更简单**：不用考虑缓存值的计算逻辑
> 2. **避免并发写覆盖**：两个线程同时更新，更新缓存会互相覆盖；删除则只会触发一次重新加载
> 3. **懒加载**：如果这个数据根本不会被读到，删了就不用重新写，省一次开销

### 4. ⚠️ Cache Aside 的并发问题与延迟双删

```
时间   线程A（写）              线程B（读）
t1    更新数据库 = 10
t2                            查缓存未命中
t3                            查数据库 = 10（旧值，A还没删缓存）
t4    删除缓存
t5                            写入缓存 = 10 ← ❌ 脏数据（应该是新值）
```

**解决方案：**
1. **延迟双删**：更新数据库 → 删缓存 → **睡一会儿** → 再删一次
2. **给缓存加 TTL**：兜底，最终一致
3. **用 Canal 监听 binlog**：异步更新缓存（本项目高级篇有讲）

### 5. 💻 本项目实现（`ShopServiceImpl.update`）

```java
@Override
@Transactional
public Result update(Shop shop) {
    Long shopId = shop.getId();
    if (shopId == null) {
        return Result.fail("店铺id不能为空");
    }
    updateById(shop);                                       // ① 先更新数据库
    stringRedisTemplate.delete(CACHE_SHOP_KEY + shopId);    // ② 再删除缓存
    return Result.ok();
}
```

### 6. 缓存穿透 🎯

**问题：** 请求查询**根本不存在**的数据，缓存永远不命中 → 每次都打到数据库 → 数据库被打垮。

**两种解决方案：**

| 方案 | 做法 | 优点 | 缺点 |
|---|---|---|---|
| **缓存空对象** | 查不到也存一个 `""`，设短 TTL | 简单 | 占内存，短期不一致 |
| **布隆过滤** | 请求先过布隆过滤器 | 不占缓存空间 | 实现复杂，有误判 |

**💻 本项目用缓存空对象：**
```java
if (shop == null) {
    // 数据库也没有 → 缓存空字符串，TTL 短一些（2分钟）
    stringRedisTemplate.opsForValue().set(CACHE_SHOP_KEY + id, "",
            CACHE_NULL_TTL, TimeUnit.MINUTES);
    return null;
}
```

**⚠️ 关键细节：** 读缓存时要用 `StrUtil.isNotBlank()` 而不是 `!= null`
```java
if (StrUtil.isNotBlank(shopJson)) {
    return JSONUtil.toBean(shopJson, Shop.class);   // 命中真实数据
}
if (shopJson != null) {
    return null;    // 空字符串 = 之前缓存过"不存在"，直接返回 null，不查库
}
```

### 7. 缓存雪崩 🎯

**问题：** **大量缓存 key 同时过期**（或 Redis 宕机）→ 所有请求同时打到数据库。

**解决方案：**
1. **给 TTL 加随机值**：`TTL = 30分钟 + random(0, 5分钟)`
2. **Redis 集群**：提高可用性
3. **服务降级熔断**：Hystrix / Sentinel
4. **多级缓存**：JVM 进程缓存兜底

### 8. 缓存击穿 🎯

**问题：** **某个热点 key 突然过期** → 大量并发请求同时打到数据库。

**⚠️ 与缓存穿透、雪崩的区别（面试必问）：**

| | 数据存在吗 | 原因 |
|---|---|---|
| **穿透** | ❌ 不存在 | 查不存在的数据 |
| **雪崩** | ✅ 存在 | **大量** key **同时**过期 |
| **击穿** | ✅ 存在 | **单个热点** key 过期 |

**两种解决方案：**

| 方案 | 思路 | 优点 | 缺点 |
|---|---|---|---|
| **互斥锁** | 只让一个线程去查库，其他等待 | 强一致 | 其他线程要等，性能下降 |
| **逻辑过期** | 不设物理 TTL，异步重建 | **不阻塞**，性能高 | 短期返回旧数据 |

### 9. 💻 互斥锁方案（`queryWithMutex`）

```java
public Shop queryWithMutex(Long id) {
    String shopJson = stringRedisTemplate.opsForValue().get(CACHE_SHOP_KEY + id);
    if (StrUtil.isNotBlank(shopJson)) {
        return JSONUtil.toBean(shopJson, Shop.class);
    }
    if (shopJson != null) {
        return null;    // 空值，直接返回
    }

    Shop shop = null;
    try {
        // 尝试获取互斥锁
        boolean b = tryLock(LOCK_SHOP_KEY + id);
        if (!b) {
            Thread.sleep(50);           // 没拿到锁 → 等一下重试
            return queryWithMutex(id);
        }
        shop = getById(id);
        if (shop == null) {
            stringRedisTemplate.opsForValue().set(CACHE_SHOP_KEY + id, "",
                    CACHE_NULL_TTL, TimeUnit.MINUTES);
            return null;
        }
        stringRedisTemplate.opsForValue().set(CACHE_SHOP_KEY + id,
                JSONUtil.toJsonStr(shop), CACHE_SHOP_TTL, TimeUnit.MINUTES);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    } finally {
        unlock(LOCK_SHOP_KEY + id);     // ⚠️ 必须在 finally 解锁
    }
    return shop;
}

private boolean tryLock(String key) {
    Boolean shop = stringRedisTemplate.opsForValue()
            .setIfAbsent(key, "1", LOCK_SHOP_TTL, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(shop);
}
```

### 10. 💻 逻辑过期方案（`queryWithLogicalExpire`）

**核心思路：** 缓存里存的不只是数据，而是包一层 `RedisData`，带一个**逻辑过期时间**；Redis 的 key **不设物理 TTL**，所以数据永不消失。

```java
public Shop queryWithLogicalExpire(Long id) {
    String shopJson = stringRedisTemplate.opsForValue().get(CACHE_SHOP_KEY + id);
    if (StrUtil.isBlank(shopJson)) {
        return null;    // 没预热过
    }
    RedisData redisData = JSONUtil.toBean(shopJson, RedisData.class);
    JSONObject data = (JSONObject) redisData.getData();
    Shop shop = JSONUtil.toBean(data, Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();

    if (expireTime.isAfter(LocalDateTime.now())) {
        return shop;    // 未过期，直接返回
    }

    // 已过期 → 抢锁，只有一个线程去重建
    boolean lock = tryLock(LOCK_SHOP_KEY + id);
    if (lock) {
        CACHE_REBUILD_EXECUTOR.submit(() -> {   // 异步重建，不阻塞当前请求
            try {
                this.saveShop2Redis(id, 20L);
            } catch (Exception e) {
                throw new RuntimeException(e);
            } finally {
                unlock(LOCK_SHOP_KEY + id);
            }
        });
    }
    return shop;    // ⚠️ 不管有没有抢到锁，都直接返回旧数据，谁都不等
}
```

**两种方案对比表（🎯 面试常问）：**

| | 互斥锁 | 逻辑过期 |
|---|---|---|
| 一致性 | **强一致** | 最终一致（返回旧数据） |
| 性能 | 有等待，吞吐低 | **无等待，吞吐高** |
| 实现复杂度 | 简单 | 复杂 |
| 适用场景 | 对一致性要求高 | **热点 key、高并发** |

### 11. 💻 封装 Redis 工具类（`CacheClient`）

把"缓存穿透"和"逻辑过期"两种通用逻辑抽成工具类，用**函数式接口**传入查库方法：

```java
public <R, ID> R queryWithPassThrough(
        String keyPrefix, ID id, Class<R> type,
        Function<ID, R> dbFallback,       // ← 函数式接口，传入查库逻辑
        Long time, TimeUnit unit) {
    String key = keyPrefix + id;
    String json = stringRedisTemplate.opsForValue().get(key);
    if (StrUtil.isNotBlank(json)) {
        return JSONUtil.toBean(json, type);
    }
    if (json != null) {
        return null;
    }
    R r = dbFallback.apply(id);           // ← 回调查库
    if (r == null) {
        stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
        return null;
    }
    this.set(key, r, time, unit);
    return r;
}
```

**调用方（`ShopServiceImpl`）：**
```java
Shop shop = cacheClient.queryWithPassThrough(
        CACHE_SHOP_KEY, id, Shop.class,
        this::getById,                    // ← 方法引用，传入查库逻辑
        CACHE_SHOP_TTL, TimeUnit.MINUTES);
```

**🎯 设计模式：模板方法 + 策略模式**
> 把"变化的"（查库逻辑）用函数式接口传进来，"不变的"（缓存读写流程）封装在工具类里。

---

## 实战篇 · 优惠券秒杀-01 ~ 08

### 1. 全局唯一 ID 🎯

**为什么需要全局唯一 ID：**
- 数据库自增 ID：**会泄露业务量**（对手能算出你一天多少单）、分库分表后不唯一
- UUID：无序，作为数据库主键**插入性能差**（B+ 树分裂）

**ID 生成策略对比：**

| 方案 | 有序性 | 性能 | 缺点 |
|---|---|---|---|
| UUID | ❌ 无序 | 高 | 主键索引效率低 |
| 数据库自增 | ✅ | 低 | 泄露业务量，单点 |
| **Redis 自增** | ✅ | 高 | 依赖 Redis |
| **雪花算法** | ✅ | 高 | 依赖时钟 |

### 2. 💻 Redis 实现全局唯一 ID（`RedisIdWorker`）

**结构：** `符号位(1) + 时间戳(31) + 序列号(32)`

```java
private static final long EPOCH = 1767196800L;   // 起始时间戳（秒）
private static final int BIT_SIZE = 32;          // 序列号位数

public long nextId(String keyPrefix) {
    // ① 时间戳（当前秒 - 起始秒）
    LocalDateTime now = LocalDateTime.now();
    long timestamp = now.toEpochSecond(ZoneOffset.of("+8")) - EPOCH;
    // ② 序列号（同一天内自增，用日期做 key 保证每天重置）
    String nowDate = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
    Long increment = stringRedisTemplate.opsForValue()
            .increment("icr:" + keyPrefix + ":" + nowDate);
    // ③ 拼接（时间戳左移 32 位 | 序列号）
    return (timestamp << BIT_SIZE) | increment;
}
```

**🎯 面试点：为什么用 `<< 32` 和 `|` 而不是字符串拼接？**
> 位运算生成的 ID 是**数字类型**，比字符串占空间小、比较快、可直接做数值运算。

**🎯 为什么序列号的 key 要带日期？**
> 让序列号**每天从 0 重新开始**，避免无限增长超过 32 位（2^32 ≈ 42 亿）。

**⚠️ 注意 ID 长度：** 时间戳 31 位 + 序列号 32 位 ≈ 2^63，生成的 ID 是 **long 级别的大数**（如 `97087344884056080`）。
前端 JavaScript 的 Number 只能精确到 2^53，**直接返回给前端会精度丢失**，需要转成 String。

### 3. 库存超卖问题 🎯

**问题：** 多个线程同时判断"库存 > 0"，都通过了判断，然后都执行扣减 → 库存变负。

```java
// ❌ 有超卖风险的写法
SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
if (voucher.getStock() <= 0) {              // ① 判断
    return Result.fail("库存不足");
}
seckillVoucherService.update()
        .setSql("stock = stock - 1")        // ② 扣减
        .eq("voucher_id", voucherId)
        .update();
```

### 4. 💻 乐观锁解决超卖

**乐观锁核心：** 在更新时**再判断一次**，用 SQL 的 WHERE 条件保证原子性。

```java
boolean voucher1 = seckillVoucherService.update()
        .setSql("stock = stock - 1")        // SET stock = stock - 1
        .eq("voucher_id", voucherId)        // WHERE voucher_id = ?
        .gt("stock", 0L)                    // AND stock > 0   ← 关键！
        .update();
if (!voucher1) {
    return Result.fail("库存不足");
}
```

**🎯 乐观锁的两种实现方式：**

| 方式 | SQL | 成功率 |
|---|---|---|
| **版本号法** | `WHERE version = 旧版本号` | 低（并发时大量失败） |
| **CAS 法** | `WHERE stock > 0` | **高**（推荐） |

**⚠️ 为什么版本号法成功率低？**
> 100 个线程同时读到 version=1，只有 1 个能更新成功，其余 99 个全失败。
> 而 `stock > 0` 只要库存还够就能成功，成功率大大提高。

**🎯 悲观锁 vs 乐观锁：**

| | 悲观锁 | 乐观锁 |
|---|---|---|
| 思想 | 先加锁再操作 | 操作时检查 |
| 实现 | `synchronized`、`SELECT ... FOR UPDATE` | CAS、版本号 |
| 适用 | 写多读少 | **读多写少** |
| 吞吐 | 低 | **高** |

### 5. 一人一单 🎯

**需求：** 同一个用户对同一张券只能下一单。

**思路：** 下单前先查该用户是否已购买过。

```java
Long count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
if (count > 0) {
    return Result.fail("用户已经购买过该优惠券");
}
```

**⚠️ 但这样有并发问题：** 同一用户开多个线程同时查，都查到 count=0，然后都下单。

**解决：** 加锁（`synchronized` 或分布式锁）

```java
// ⚠️ 不能锁 this（整个对象），粒度太粗
synchronized (userId.toString().intern()) {   // 锁用户 ID
    // ⚠️ 必须用代理对象调用，否则 @Transactional 失效！
    IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
    return proxy.createVoucherOrder(voucherId);
}
```

**🎯 面试点 1：`synchronized (userId.toString().intern())` 为什么要 `.intern()`？**
> `userId.toString()` **每次都会 new 一个新 String 对象**，直接锁它在不同线程里是**不同的锁对象**，锁不住。
> `.intern()` 会从**字符串常量池**取，相同内容的字符串返回**同一个对象**，才能锁住。

**🎯 面试点 2：为什么必须用 `AopContext.currentProxy()`？**
> Spring 的 `@Transactional` 基于 **AOP 代理**。
> `this.createVoucherOrder()` 是**类内部调用**，不走代理 → **事务失效**。
> 用 `AopContext.currentProxy()` 拿到代理对象再调用，事务才生效。

### 6. ⚠️ 集群下的线程并发安全问题

`synchronized` 只在**单个 JVM** 内有效。集群部署时，多个 JVM 各有各的锁 → **锁不住**。

**解决：** 用**分布式锁**（下一个主题）。

---

## 实战篇 · 分布式锁-09 ~ 21 🎯

### 1. 分布式锁的基本原理

**核心：** 用**外部共享存储**（Redis / Zookeeper / MySQL）记录锁状态，所有 JVM 都去访问它。

**必须满足的四个条件：**
1. **互斥**：任意时刻只有一个线程持有锁
2. **防死锁**：锁能自动释放（过期时间）
3. **解铃还须系铃人**：谁加的锁谁解
4. **可重入**（可选）：同一线程可重复获取

### 2. 三种实现方案对比

| 方案 | 实现 | 优点 | 缺点 |
|---|---|---|---|
| **MySQL** | 唯一索引 / `FOR UPDATE` | 简单 | 性能差，易死锁 |
| **Redis** | `SETNX` + `EXPIRE` | **性能高** | 实现复杂 |
| **ZooKeeper** | 临时顺序节点 | 可靠性高 | 性能不如 Redis |

### 3. 💻 实现思路（`SimpleRedisLock`）

```java
private static final String KEY_PREFIX = "lock:";
private static final String ID_PREFIX = UUID.randomUUID().toString() + "-";

@Override
public boolean tryLock(Long timeoutSec) {
    // 线程标识（UUID + 线程ID），用于判断锁是不是自己的
    String threadId = ID_PREFIX + Thread.currentThread().getId();
    Boolean success = stringRedisTemplate.opsForValue()
            .setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
    return Boolean.TRUE.equals(success);
}

@Override
public void unlock() {
    // 用 Lua 脚本保证"判断+删除"的原子性
    stringRedisTemplate.execute(UNLOCK_SCRIPT,
            Collections.singletonList(KEY_PREFIX + name),
            ID_PREFIX + Thread.currentThread().getId());
}
```

### 4. ⚠️ 三个渐进式问题（面试重点）

#### 问题 1：误删别人的锁

```
时间   线程A              线程B
t1    获取锁（超时10s）
t2    业务阻塞...
t3    锁自动过期 → 锁没了
t4                       获取锁成功
t5    A执行完，unlock() → 删掉了B的锁！❌
```

**解决：锁的 value 存线程标识，删除前先判断是不是自己的**

```java
String threadId = ID_PREFIX + Thread.currentThread().getId();
String s = stringRedisTemplate.opsForValue().get(KEY_PREFIX + name);
if (threadId.equals(s)) {                       // 判断是自己的锁
    stringRedisTemplate.delete(KEY_PREFIX + name);
}
```

#### 问题 2：判断和删除不是原子的

上面"先判断、再删除"是**两条命令**，中间可能被打断：

```
t1  A判断是自己的锁 ✅
t2  A的锁过期，B拿到锁
t3  A删除 → 又删掉了B的锁！❌
```

**解决：用 Lua 脚本保证原子性**

```lua
-- unlock.lua
if(redis.call('get',KEYS[1]) == ARGV[1]) then
    return redis.call('del',KEYS[1])
end
return 0
```

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;

static {
    UNLOCK_SCRIPT = new DefaultRedisScript<>();
    UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
    UNLOCK_SCRIPT.setResultType(Long.class);
}
```

**🎯 为什么 Lua 能保证原子性？**
> Redis **单线程**执行命令，执行 Lua 脚本时会**把整个脚本当作一个命令**，中间不会插入其他命令。

**⚠️ 踩坑记录：Lua 脚本文件路径**
```java
UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
```
如果文件实际在 `src/main/resources/mapper/unlock.lua`，编译后在 `target/classes/mapper/unlock.lua`，
但代码找的是 `classpath:unlock.lua`（**根目录**）→ 启动报错：
```
FileNotFoundException: class path resource [unlock.lua] cannot be opened
```
**解决：** 路径必须和文件实际位置一致（`"mapper/unlock.lua"` 或把文件挪到根目录）。

#### 问题 3：锁超时释放导致的并发

A 的业务执行时间 > 锁的过期时间 → 锁提前释放 → B 拿到锁 → **两个线程同时执行**。

**解决：** **Redisson** 的看门狗机制（下面讲）。

### 5. Redisson 介绍 🎯

**Redisson 是什么：** 在 Redis 基础上实现的**分布式对象、分布式集合**框架，提供各种分布式工具。

**核心优势：**
- **可重入锁**
- **看门狗（WatchDog）自动续期**
- **锁重试**
- **MultiLock**（联锁）

### 6. 💻 Redisson 使用

**⚠️ 依赖选择（本项目踩过的坑）：**
```xml
<!-- ❌ 不要用这个 starter：会带入 redisson-spring-data-32，与 Spring Data Redis 3.5 不兼容 -->
<!-- 会导致 expire 无限递归 StackOverflowError -->
<artifactId>redisson-spring-boot-starter</artifactId>

<!-- ✅ 只用核心包 -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.27.2</version>
</dependency>
```

**配置：**
```java
@Configuration
public class RedissonConfig {
    @Bean
    public RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer().setAddress("redis://localhost:6379");
        return Redisson.create(config);
    }
}
```

**使用：**
```java
RLock lock = redissonClient.getLock("lock:order:" + userId);
boolean isLock = lock.tryLock();      // 不传参：拿不到立即返回 false
if (!isLock) {
    return Result.fail("请勿重复下单");
}
try {
    // 业务逻辑
} finally {
    lock.unlock();                    // ⚠️ 必须在 finally 释放
}
```

### 7. Redisson 可重入锁原理 🎯

**核心：用 Hash 结构存"锁名 → (线程标识 → 重入次数)"**

```lua
-- 加锁 Lua
-- KEYS[1]=锁名  ARGV[1]=过期时间  ARGV[2]=线程标识
if (redis.call('exists', KEYS[1]) == 0) then
    redis.call('hincrby', KEYS[1], ARGV[2], 1)   -- 首次加锁，计数=1
    redis.call('pexpire', KEYS[1], ARGV[1])
    return nil
end
if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then
    redis.call('hincrby', KEYS[1], ARGV[2], 1)   -- 重入，计数+1
    redis.call('pexpire', KEYS[1], ARGV[1])
    return nil
end
return redis.call('pttl', KEYS[1])               -- 被别人锁着，返回剩余时间

-- 解锁 Lua
if (redis.call('hexists', KEYS[1], ARGV[3]) == 0) then
    return nil                                    -- 不是自己的锁
end
local count = redis.call('hincrby', KEYS[1], ARGV[3], -1)   -- 计数-1
if (count > 0) then
    redis.call('pexpire', KEYS[1], ARGV[1])       -- 还有重入，续期
    return 0
else
    redis.call('del', KEYS[1])                    -- 计数=0，真正释放
    return 1
end
```

**🧠 记忆：** **Hash 的 field 是线程标识，value 是重入次数；每加锁 +1，每解锁 -1，减到 0 才删 key**

### 8. Redisson 锁重试 + WatchDog 机制 🎯

#### 锁重试

```java
// 不传参：拿不到立刻返回 false
lock.tryLock();

// 传参：最多等 waitTime，锁超时 leaseTime
lock.tryLock(10, TimeUnit.SECONDS);
```
底层用 **Pub/Sub 订阅**其他线程释放锁的消息，而不是**轮询**（节省 CPU）。

#### WatchDog（看门狗）自动续期 🎯

**问题：** 如果业务执行时间超过锁的过期时间，锁会自动释放 → 并发问题。

**Redisson 的解决：**
- 使用 `tryLock()` 或 `tryLock(waitTime, TimeUnit)` **不指定 leaseTime** 时
- Redisson 会启动一个**定时任务**（看门狗），每隔 `releaseTime/3`（默认锁时间 30s，即每 10s）
- **重置锁的过期时间为 30s**
- 直到**主动 unlock** 或**服务宕机**（看门狗线程停止，锁自动过期）

**⚠️ 关键：一旦指定了 `leaseTime`，看门狗机制就失效了！**
```java
lock.tryLock(10, 60, TimeUnit.SECONDS);   // 指定了 leaseTime=60s → 不续期
```

### 9. Redisson MultiLock 联锁 🎯

**需求：** 多个 Redis 节点（主从/集群）下，要保证锁的**高可靠**——在**所有节点**上同时加锁。

```java
RLock lock1 = redissonClient.getLock("lock1");
RLock lock2 = redissonClient.getLock("lock2");
RLock lock3 = redissonClient.getLock("lock3");

// 联锁：三个锁都成功才算成功
RLock multiLock = redissonClient.getMultiLock(lock1, lock2, lock3);
multiLock.lock();
```

**用联锁有什么问题？**
- **成本高**：要在多个节点操作
- **可用性差**：任何一个节点挂掉，锁就加不上
- 一般用于**对可靠性要求极高**的场景

---

## 实战篇 · 秒杀优化-22 ~ 24 + Redis 消息队列-25 ~ 30

### 1. 异步秒杀思路 🎯

**同步秒杀的问题：** 每个请求都要：查优惠券 → 判断时间 → 判断库存 → 查是否重复购买 → 扣库存 → 创建订单。
**大量数据库操作**，并发上不去。

**优化思路：把"判断秒杀资格"和"下单"分开**

```
用户请求
  ↓
① Redis 中原子判断：库存 + 一人一单     ← 快，Lua 脚本
  ↓ 通过
② 返回"抢购成功"，把订单扔进队列         ← 快，立即返回
  ↓
③ 后台线程从队列取订单，慢慢落库          ← 慢，但不阻塞用户
```

**🎯 性能提升：** 用户响应时间从"一次数据库事务"降到"一次 Redis 调用"。

### 2. 💻 Lua 脚本完成秒杀资格判断（`seckill.lua`）

```lua
local voucherId = ARGV[1]
local userId = ARGV[2]
local orderId = ARGV[3]

local stockKey = "seckill:stock:" .. voucherId
local orderKey = "seckill:order:" .. voucherId

-- ① 判断库存
if (tonumber(redis.call('get', stockKey)) <= 0) then
    return 1                              -- 库存不足
end

-- ② 判断是否重复下单（Set 结构）
if (redis.call('sismember', orderKey, userId) == 1) then
    return 2                              -- 重复下单
end

-- ③ 扣库存 + 记录用户
redis.call('incrby', stockKey, -1)
redis.call('sadd', orderKey, userId)

-- ④ 发送消息到 Stream 队列
redis.call('xadd', 'stream.orders', '*',
        'voucherId', voucherId, 'userId', userId, 'id', orderId)
return 0                                  -- 成功
```

**返回码约定：** `0` = 成功，`1` = 库存不足，`2` = 重复下单

### 3. 💻 Java 调用

```java
@Override
public Result seckillVoucher(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    long orderId = redisIdWorker.nextId("order");

    Long execute = stringRedisTemplate.execute(
            SECKILL_SCRIPT,
            Collections.emptyList(),                    // ⚠️ KEYS 传空
            voucherId.toString(), userId.toString(), String.valueOf(orderId)
    );
    int i = execute.intValue();
    if (i != 0) {
        return Result.fail(i == 1 ? "库存不足" : "请勿重复下单");
    }
    return Result.ok(orderId);                       // 立即返回，不等落库
}
```

**🎯 面试点：为什么把 `voucherId`、`userId` 放在 `ARGV` 而不是 `KEYS`？**
> Redis 集群下，`KEYS` 用来计算**分片槽位**，同一个脚本的所有 KEYS 必须在**同一个槽**。
> 如果放 KEYS，不同券会落到不同节点 → 报错 `CROSSSLOT`。
> 放 ARGV 就没有这个限制，同一个脚本能被多个券复用。

### 4. Redis 消息队列 🎯

#### 三种方案对比（面试重点）

| 方案 | 原理 | 优点 | 缺点 |
|---|---|---|---|
| **List** | `LPUSH` + `BRPOP` | 简单、消息可持久 | **不支持一对多**（一条消息只能被一个消费者拿） |
| **PubSub** | 发布订阅 | 支持一对多 | **消息不持久**，消费者不在线就丢消息 |
| **Stream** | 专门的 MQ 结构 | **持久化 + 支持消费组 + ACK 确认** | 结构复杂 |

#### Stream 单消费模式

```bash
XADD stream.orders * voucherId 12 userId 1    # 发消息
XLEN stream.orders                             # 队列长度
XRANGE stream.orders - +                       # 读所有
XREAD COUNT 1 STREAMS stream.orders 0          # 读一条（0 表示从头读）
XREAD COUNT 1 BLOCK 2000 STREAMS stream.orders $  # 阻塞读，只读新消息
```

**⚠️ `$` 和 `0` 的区别：**
- `0`：从头读（会重复读已读过的）
- `$`：只读**最新**的消息

#### Stream 消费者组模式 🎯

```bash
# ① 创建消费组（⚠️ 必须先创建！）
XGROUP CREATE stream.orders g1 0 MKSTREAM

# ② 消费者从组里读
XREADGROUP GROUP g1 c1 COUNT 1 STREAMS stream.orders >

# ③ 确认消息已处理
XACK stream.orders g1 {消息ID}

# ④ 查看未确认的消息
XPENDING stream.orders g1
```

**🎯 `>` 的含义：** 从**最后一个被投递的消息之后**开始读（保证每条消息只被组内一个消费者读到）。

**⚠️ 踩坑记录（本项目真实遇到的严重 bug）：**

**症状：** 应用启动后日志**疯狂刷屏**，5 秒刷 3600 行：
```
ERROR c.h.s.impl.VoucherOrderServiceImpl : 处理挂起订单列表发生异常
```

**原因：**
1. `seckill.lua` 里的 `XADD` **只创建 stream，不创建消费组**
2. 而 Java 代码用 `Consumer.from("g1","c1")` 读 → Redis 报错：
   ```
   NOGROUP No such key 'stream.orders' or consumer group 'g1' in XREADGROUP with GROUP option
   ```
3. 异常被 catch 后调用 `handlePendingList()`，而它内部是 `while(true)` + `sleep(20)` → **永久死循环**

**解决：在 `init()` 里先创建消费组，再启动消费者**
```java
@PostConstruct
public void init() {
    try {
        stringRedisTemplate.opsForStream()
                .createGroup(queueName, ReadOffset.from("0"), "g1");
        log.info("消费组 g1 创建成功");
    } catch (Exception e) {
        log.info("消费组 g1 已存在，跳过创建：{}", e.getMessage());   // BUSYGROUP 属正常
    }
    SECKILL_ORDEr_EXECUTOR.submit(new VoucherOrderHandler());
}
```

**⚠️ 附带教训：`log.error("处理挂起订单列表发生异常")` 没传异常对象**

```java
// ❌ 只打消息，看不到真实异常类型（NOGROUP 就是这样被吞掉的）
log.error("处理挂起订单列表发生异常");

// ✅ 带上异常对象
log.error("处理挂起订单列表发生异常", e);
```
**🧠 记忆：`log.error` 一定要把异常对象 `e` 作为最后一个参数传进去！**

### 5. 💻 基于 Stream 实现异步秒杀

**消费者：**
```java
private class VoucherOrderHandler implements Runnable {
    @Override
    public void run() {
        while (true) {
            try {
                // ① 从队列读一条消息（阻塞 2 秒）
                List<MapRecord<String, Object, Object>> list =
                        stringRedisTemplate.opsForStream().read(
                                Consumer.from("g1", "c1"),
                                StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                                StreamOffset.create(queueName, ReadOffset.lastConsumed())
                        );
                if (list == null || list.isEmpty()) {
                    continue;
                }
                // ② 解析并处理订单
                MapRecord<String, Object, Object> entries = list.get(0);
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(
                        entries.getValue(), new VoucherOrder(), true);
                handleVoucherOrder(voucherOrder);
                // ③ 确认消息（ACK）
                stringRedisTemplate.opsForStream()
                        .acknowledge(queueName, "g1", entries.getId());
            } catch (Exception e) {
                log.error("订单处理线程发生异常，转入处理挂起订单", e);
                handlePendingList();
            }
        }
    }
}
```

**🎯 为什么需要 ACK 机制？**
> 消息被消费者读取后，会进入 **pending（待确认）列表**。
> 只有 `XACK` 后才算真正处理完成。
> 如果消费者处理到一半挂了，消息仍在 pending 里，可以用 `handlePendingList()` 重新处理 → **消息不丢失**。

### 6. ⚠️ 踩坑记录：`AopContext.currentProxy()` 在后台线程失效

**症状：**
```
IllegalStateException: Cannot find current proxy: Set 'exposeProxy' property on Advised to 'true' to make it available,
and ensure that AopContext.currentProxy() is invoked in the same thread as the AOP invocation context.
    at VoucherOrderServiceImpl.handleVoucherOrder(VoucherOrderServiceImpl.java:288)
```

**原因：** `AopContext.currentProxy()` 依赖 **ThreadLocal**，只有"当前线程经过了代理入口"时才有值。
`handleVoucherOrder` 跑在**后台线程** `pool-5-thread-1`，这个线程从未经过 AOP 代理 → ThreadLocal 是空的。

**解决：改成自身注入（Spring 会注入代理对象）**
```java
@Resource
@Lazy                                   // 打断"自己依赖自己"的循环依赖
private IVoucherOrderService selfProxy;

// 使用
selfProxy.createVoucherOrder(voucherOrder);
```

**🧠 记忆：后台线程不能用 `AopContext.currentProxy()`，要提前把代理注入成字段。**

---

## 实战篇 · 达人探店-01 ~ 04

### 1. 点赞功能 🎯

**需求：** 一个用户对同一篇博客只能点赞一次；点赞后显示"已点赞"；可以取消。

**数据模型：** 用 **Set** 存"哪些用户点赞了这篇博客"
```
key:   blog:liked:{blogId}
value: {userId1, userId2, ...}
```

**💻 本项目实现：**
```java
@Override
public Result likeBlog(Long id) {
    Long userId = UserHolder.getUser().getId();
    String key = BLOG_LIKED_KEY + id;
    // ① 判断是否已点赞
    Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
    if (BooleanUtil.isFalse(isMember)) {
        // 未点赞 → 点赞：数据库 +1，Redis 记录
        boolean success = update().setSql("liked = liked + 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForSet().add(key, userId.toString());
        }
    } else {
        // 已点赞 → 取消：数据库 -1，Redis 移除
        boolean success = update().setSql("liked = liked - 1").eq("id", id).update();
        if (success) {
            stringRedisTemplate.opsForSet().remove(key, userId.toString());
        }
    }
    return Result.ok();
}
```

**⚠️ 为什么用 Set 而不是直接改数据库的 liked 字段？**
> 数据库只存**总数**，不知道"谁点过"，无法判断当前用户是否已点赞、无法取消。
> Redis Set 存的是**用户 ID 集合**，天然去重、O(1) 判断。

### 2. 点赞排行榜 🎯

**需求：** 按**点赞时间**排序，显示前 5 个点赞的用户。

**问题：** Set 是**无序**的，做不到按时间排序！

**解决：改用 ZSet，score = 时间戳**
```
key:   blog:liked:{blogId}
value: {userId1: 时间戳1, userId2: 时间戳2, ...}
```

**💻 本项目实现：**
```java
// 点赞
stringRedisTemplate.opsForZSet().add(key, userId.toString(), System.currentTimeMillis());

// 查前 5 名（按 score 升序 = 按时间先后）
Set<String> top5 = stringRedisTemplate.opsForZSet().range(key, 0, 4);
// 查用户的 score（判断当前用户是否点过赞）
Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
```

**🎯 面试点：为什么 score 用时间戳？**
> ZSet 按 score 自动排序 → 时间戳越大越靠后 → **天然实现按时间排序**。

**⚠️ 查用户信息要保持顺序：**
```java
// ZSet 返回的是有序的 ID 集合，但 SQL 的 IN 查询不保证顺序
// 需要手动按 ZSet 的顺序重排
List<UserDTO> userDTOS = userIds.stream()
        .map(id -> userMap.get(id))     // 用 Map 保序
        .collect(Collectors.toList());
```

---

## 实战篇 · 好友关注-05 ~ 10

### 1. 关注和取关

```java
@Override
public Result follow(Long followUserId, boolean isFollow) {
    Long userId = UserHolder.getUser().getId();
    if (isFollow) {
        Follow follow = new Follow();
        follow.setUserId(userId);
        follow.setFollowUserId(followUserId);
        save(follow);                                       // 插入关注记录
    } else {
        remove(new QueryWrapper<Follow>()
                .eq("user_id", userId)
                .eq("follow_user_id", followUserId));       // 删除关注记录
    }
    return Result.ok();
}
```

### 2. 共同关注 🎯

**需求：** 查询两个用户的共同关注。

**思路：** 用 **Set 求交集**（`SINTER`）

```java
// 关注时：把被关注者加入当前用户的关注 Set
stringRedisTemplate.opsForSet().add(FOLLOW_KEY + userId, followUserId.toString());

// 共同关注：
Set<String> intersect = stringRedisTemplate.opsForSet()
        .intersect(FOLLOW_KEY + userId1, FOLLOW_KEY + userId2);
```

### 3. Feed 流（关注推送）🎯

**需求：** 用户发布博客后，推送给所有粉丝，粉丝能刷到。

**两种模式对比（面试重点）：**

| | **Timeline（推模式）** | **收件箱（拉模式）** |
|---|---|---|
| 做法 | 发博客时**主动推**给所有粉丝 | 粉丝**主动拉**取关注的人的博客 |
| 优点 | **读快**（直接读自己的收件箱） | 写快，不占存储 |
| 缺点 | 写扩散（大 V 发一条要推给百万粉丝） | **读慢**（要合并多个关注人的博客） |
| 适用 | **普通用户**（粉丝少） | 大 V |

**本项目用推模式：**

```java
// 发布博客时，推送给所有粉丝
List<Follow> follows = followService.query().eq("follow_user_id", userId).list();
for (Follow follow : follows) {
    String key = FEED_KEY + follow.getUserId();
    stringRedisTemplate.opsForZSet().add(key, blog.getId().toString(), System.currentTimeMillis());
}
```

### 4. 滚动分页查询 🎯

**问题：** ZSet 用 `ZREVRANGE key (page-1)*size page*size-1` 分页，会导致**数据重复**——
因为新发布的博客会插入到最前面，导致后面的数据整体后移。

**解决：用 `ZREVRANGEBYSCORE` 按 score 游标分页**

```java
// 第一次查询：max = 当前时间戳
// 后续查询：max = 上一页最后一条的 score
Set<ZSetOperations.TypedTuple<String>> typedTuples = stringRedisTemplate
        .opsForZSet()
        .reverseRangeByScoreWithScores(key, 0, max, offset, 2);   // offset 处理同分情况
```

**🎯 核心参数：**
- `max`：上一页最后一条的 **score**（时间戳）
- `offset`：**与上一页最后一条 score 相同的元素个数**（因为可能有多条记录 score 相同）

**⚠️ 为什么要 offset？** 如果有多条博客的 score（时间戳）完全相同，只传 max 会漏掉或重复。

---

## 实战篇 · 附近商铺-GEO-11 ~ 13

### GEO 数据结构

```bash
GEOADD shop:geo:1 120.149192 30.316078 1        # 添加（经度 纬度 成员）
GEODIST shop:geo:1 1 2 km                        # 计算两点距离
GEOHASH shop:geo:1 1                             # 转成 hash 字符串
GEOPOS shop:geo:1 1                              # 查坐标
GEOSEARCH shop:geo:1 FROMLONLAT 120.149 30.316 BYRADIUS 5 km ASC COUNT 10
```

### 💻 附近商铺实现

```java
// 导入店铺坐标
stringRedisTemplate.opsForGeo().add(key, new Point(x, y), shopId.toString());

// 查询附近
GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate
        .opsForGeo()
        .search(key,
                GeoReference.fromCoordinate(x, y),        // 以 (x,y) 为中心
                new Distance(5000),                       // 5km 内
                RedisGeoCommands.GeoSearchCommandArgs
                        .newGeoSearchArgs()
                        .includeDistance()                // 返回距离
                        .limit(end));
```

**🎯 面试点：GEO 底层用什么数据结构？**
> **SortedSet**。把经纬度编码成一个 **52 位整数**作为 score，所以 GEO 的成员也是唯一的。

**⚠️ 编码方式：GeoHash** —— 把二维经纬度用**二分法**不断切分，最终编码成一个整数。
优点是**位置相近的点编码前缀也相近**，缺点是**边界问题**（两个相邻的点可能编码差异很大）。

---

## 实战篇 · 用户签到-BitMap-15 ~ 17

### 1. BitMap 介绍

**本质：** 用 **bit 位**存储，每个 bit 只有 0/1。

**优势：** 极其省内存。记录 1 亿用户的签到状态，只需 **12MB**（1亿 bit ≈ 12.5MB）。

```bash
SETBIT sign:1 0 1        # 第 0 位设为 1
GETBIT sign:1 0          # 读第 0 位
BITCOUNT sign:1          # 统计 1 的个数
BITFIELD sign:1 GET u2 0 # 取 2 位
```

### 2. 💻 实现签到功能

```java
@Override
public Result sign() {
    Long userId = UserHolder.getUser().getId();
    LocalDateTime now = LocalDateTime.now();
    // key: sign:{userId}:{年月}
    String key = USER_SIGN_KEY + userId + ":" + now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    // 用"今天是本月第几天 - 1"作为 bit 下标
    int offset = now.getDayOfMonth() - 1;
    stringRedisTemplate.opsForValue().setBit(key, offset, true);
    return Result.ok();
}
```

**🎯 key 设计：** `sign:{userId}:{yyyyMM}` —— 每个月一个 key，避免单个 BitMap 无限增长。

### 3. 💻 统计连续签到天数

**思路：** 从今天往前数，遇到第一个 0 就停。

```java
@Override
public Result signCount() {
    Long userId = UserHolder.getUser().getId();
    LocalDateTime now = LocalDateTime.now();
    String key = USER_SIGN_KEY + userId + now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    int dayOfMonth = now.getDayOfMonth();

    // ① 取本月截至今天的全部签到记录（BITFIELD 返回十进制数）
    List<Long> result = stringRedisTemplate.opsForValue().bitField(
            key,
            BitFieldSubCommands.create()
                    .get(BitFieldSubCommands.BitFieldType.unsigned(dayOfMonth))
                    .valueAt(0)
    );
    if (result == null || result.isEmpty()) {
        return Result.ok(0);
    }
    Long num = result.get(0);
    if (num == null || num == 0) {
        return Result.ok(0);
    }

    // ② 循环与 1 做与运算，统计连续 1 的个数
    int count = 0;
    while (true) {
        if ((num & 1) == 0) {     // 最低位是 0 → 没签到，结束
            break;
        } else {
            count++;
        }
        num >>>= 1;               // 无符号右移一位
    }
    return Result.ok(count);
}
```

**🎯 关键点：**
- `BITFIELD` 可以一次读取**多个 bit** 并转成十进制数（比循环 `GETBIT` 快得多）
- `>>>` 是**无符号右移**（`>>` 会保留符号位，负数会死循环）
- `num & 1` 取最低位，判断是否为 1

---

## 实战篇 · UV 统计-HyperLogLog-18 ~ 19

### 1. HyperLogLog 介绍

**用途：** **基数统计**（统计不重复元素的个数），如 UV（独立访客）。

**特点：**

| 优点 | 缺点 |
|---|---|
| **极省内存**（固定 12KB 可统计 2^64 个元素） | **有误差**（标准误差 0.81%） |
| 支持合并（`PFMERGE`） | 只能统计基数，**不能查具体元素** |

```bash
PFADD uv:20260101 user1 user2 user3    # 添加
PFCOUNT uv:20260101                     # 统计基数
PFMERGE uv:total uv:day1 uv:day2       # 合并
```

**🎯 面试点：HyperLogLog 为什么省内存？**
> 基于**概率算法**（调和平均数 + 分桶）。
> 原理：通过观察哈希值中**第一个 1 出现的位置**来估算基数——如果出现了很长的前导 0，说明元素数量很大。
> 用 16384 个桶（2^14），每个桶 6 bit 存最大前导零数，共 12KB。

**⚠️ 和 BitMap 的区别：**

| | BitMap | HyperLogLog |
|---|---|---|
| 存储 | 每个元素对应 1 bit | 固定 12KB |
| 精确性 | **精确** | 有 0.81% 误差 |
| 适用 | 元素范围小且连续（如用户 ID 0~1亿） | **元素范围大**（如 UUID、IP） |

---

# 高级篇

## 高级篇 · 分布式缓存

### 1. Redis 持久化 🎯

#### RDB（Redis DataBase）

**原理：** 把内存数据**快照**写入磁盘（二进制文件 `dump.rdb`）。

```conf
save 900 1      # 900秒内有1个key变化就触发
save 300 10     # 300秒内有10个key变化
save 60 10000   # 60秒内有10000个key变化
```

**命令：**
```bash
SAVE      # 阻塞主线程（❌ 生产禁用）
BGSAVE    # fork 子进程执行（✅ 推荐）
```

**⚠️ fork 原理：**

| | 说明 |
|---|---|
| **fork 是什么** | Linux 系统调用，**复制主进程**（页表），创建子进程 |
| **为什么快** | 只复制**页表**，不复制物理内存（**写时复制 COW**） |
| **阻塞吗** | fork 本身会阻塞主进程，但时间很短（与页表大小有关） |

**🎯 面试点：RDB 的 fork 会阻塞吗？**
> 会，但只阻塞**页表复制**的时间。数据量越大、页表越多，阻塞越久。
> 一般 1GB 数据约阻塞 20ms 左右。

#### AOF（Append Only File）

**原理：** 记录**每一条写命令**（追加到 `appendonly.aof`）。

```conf
appendonly yes
appendfsync always      # 每条命令都刷盘（最安全，最慢）
appendfsync everysec    # 每秒刷盘（默认，推荐）
appendfsync no          # 交给操作系统（最快，最不安全）
```

**AOF 文件重写：** AOF 会越来越大，需要**重写**压缩
```conf
auto-aof-rewrite-percentage 100    # 比上次重写后增长 100% 就重写
auto-aof-rewrite-min-size 64mb     # 且文件至少 64MB
```

#### RDB vs AOF 对比 🎯

| | RDB | AOF |
|---|---|---|
| **数据安全性** | 差（可能丢最后一次快照后的数据） | 好（最多丢 1 秒） |
| **文件大小** | 小（二进制压缩） | 大（记录所有命令） |
| **恢复速度** | **快** | 慢（要重放所有命令） |
| **性能影响** | fork 时阻塞 | 刷盘有开销 |
| **适用** | 备份、灾难恢复 | 数据安全性要求高 |

**🎯 生产环境建议：两者都开**
> RDB 用于快速恢复和备份，AOF 用于保证数据不丢。
> Redis 4.0+ 支持**混合持久化**（`aof-use-rdb-preamble yes`）：AOF 文件前半部分是 RDB 格式，后半部分是 AOF 命令 → 兼顾恢复速度和安全性。

### 2. Redis 主从复制 🎯

#### 主从集群结构

```
       ┌─────────┐
       │  Master │  ← 写
       └────┬────┘
     ┌──────┼──────┐
     ↓      ↓      ↓
  Slave1  Slave2  Slave3   ← 读
```

**作用：**
1. **读写分离**：master 写、slave 读，提高并发
2. **数据备份**：slave 是 master 的副本
3. **故障恢复**：master 挂了，slave 可以顶上（配合哨兵）

#### 全量同步 🎯

**触发时机：** slave **第一次**连接 master

```
① slave 请求同步 → 发送 replid 和 offset
② master 判断 replid 不一致 → 是第一次同步
③ master 执行 bgsave 生成 RDB
④ master 发送 RDB 给 slave
⑤ slave 清空本地数据，加载 RDB
⑥ master 把生成 RDB 期间的命令写入 repl_baklog，发给 slave
```

**🎯 关键概念：**

| 概念 | 说明 |
|---|---|
| **replid** | 数据集的**唯一标识**，master 重启或 slave 提升会生成新的 |
| **offset** | 偏移量，记录同步到哪里 |

#### 增量同步

**触发时机：** slave **断线重连**，且 replid 一致（同一个 master）

```
① slave 发送自己的 offset
② master 从 repl_baklog 中找 offset 之后的数据
③ 只发送差异部分
```

**⚠️ 如果 offset 已经被覆盖（环形数组写满）？** → 退化为**全量同步**。

**🎯 面试点：repl_baklog 是什么？**
> 一个**固定大小的环形数组**（默认 1MB）。
> master 记录所有写命令，slave 断线重连后从里面找差异数据。
> 如果 slave 落后太多（数据被覆盖），只能全量同步。

### 3. Redis 哨兵（Sentinel）🎯

**作用：**
1. **监控**：不断检查 master 和 slave 是否正常
2. **自动故障恢复**：master 挂了，从 slave 中选一个当新 master
3. **通知**：把新 master 地址告诉客户端

#### 哨兵集群

哨兵本身也是集群，避免单点故障。

**🎯 主观下线 vs 客观下线：**

| | 说明 |
|---|---|
| **主观下线（SDOWN）** | **单个**哨兵认为节点挂了 |
| **客观下线（ODOWN）** | **超过 quorum 个**哨兵都认为 master 挂了 |

**⚠️ 为什么需要客观下线？** 避免**误判**（网络抖动导致单个哨兵连不上 master）。

#### 选新 master 的规则

```
① 判断 slave 与 master 断开的时间，超过 down-after-milliseconds * 10 次 → 排除
② 判断 slave 的 priority（优先级），越小优先级越高
③ 判断 offset，越大说明数据越新
④ 判断 runid，越小越优先
```

**🎯 记忆顺序：** **断开时间 → 优先级 → offset → runid**

#### 故障转移流程

```
① 哨兵选出一个 slave 作为新 master
② 让该 slave 执行 slaveof no one（变成 master）
③ 让其他 slave 执行 slaveof 新master
④ 修改故障节点的配置，重启后变成 slave
```

### 4. Redis 分片集群 🎯

**为什么需要分片集群：**
- 主从 + 哨兵解决了**高可用**，但没解决**海量数据存储**（单机内存有限）
- 也没有解决**高并发写**（master 只有一个）

**分片集群特点：**
- 多个 master，每个存一部分数据
- 每个 master 可以有多个 slave
- master 之间**互相 ping** 检测健康
- 客户端可访问任意节点，最终转发到正确节点

#### 散列插槽（Hash Slot）🎯

**总共 16384 个插槽**（`0 ~ 16383`）。

```
slot = CRC16(key) % 16384
```

**🎯 面试点：为什么是 16384 而不是 65536？**
> 1. **16384 个槽的位图只占 2KB**，65536 要占 8KB，心跳包会太大
> 2. Redis 集群节点数一般不超过 1000，16384 个槽足够分配
> 3. 位图压缩率高

**⚠️ 关键：如果 key 中有 `{}`，只对 `{}` 内的内容计算插槽**
```
{user}:1  →  按 "user" 算槽
{user}:2  →  按 "user" 算槽    ← 两条数据在同一个槽！
```
**用途：** 让需要同时操作的 key 落在同一节点（便于用 Lua 脚本、事务）。

**⚠️ 踩坑记录：** 这就解释了为什么秒杀 Lua 脚本要把 `voucherId`、`userId` 放在 **ARGV** 而不是 KEYS —— 否则不同券会算到不同槽，跨槽操作会报 `CROSSSLOT` 错误。

#### 集群伸缩

```bash
# 添加节点
redis-cli --cluster add-node 新节点IP:端口 已有节点IP:端口

# 重新分片（转移插槽）
redis-cli --cluster reshard 任意节点IP:端口
```

#### 故障转移

master 挂掉后，其 slave 会**自动升级为 master**（不需要哨兵）：
```
① 各 master 之间 ping，发现某 master 失联
② 该 master 的 slave 发现自己的 master 挂了
③ slave 发起选举，其他 master 投票
④ 获得超过半数票的 slave 成为新 master
```

---

## 高级篇 · 多级缓存

### 1. 什么是多级缓存

**传统缓存的问题：** 所有请求都打到 Tomcat，Tomcat 成为瓶颈。

**多级缓存：** 在**每个环节**都加缓存
```
浏览器缓存
  ↓
Nginx 本地缓存（Lua + shared dict）
  ↓
Redis 缓存
  ↓
JVM 进程缓存（Caffeine）
  ↓
Tomcat → 数据库
```

**优势：** 逐级过滤请求，越靠前拦截越好。

### 2. JVM 进程缓存（Caffeine）🎯

**Caffeine：** 基于 Java 8 的高性能本地缓存库。

```java
Cache<String, String> cache = Caffeine.newBuilder()
        .initialCapacity(100)       // 初始容量
        .maximumSize(10000)         // 最大容量（超过会淘汰）
        .expireAfterWrite(Duration.ofMinutes(10))   // 写入后过期
        .build();
```

**🎯 Caffeine vs Redis：**

| | Caffeine（进程缓存） | Redis（分布式缓存） |
|---|---|---|
| 位置 | **JVM 内存** | 独立的服务 |
| 速度 | **更快**（无网络开销） | 快（有网络 IO） |
| 一致性 | 多实例间**不一致** | 全局一致 |
| 容量 | 受 JVM 堆限制 | 可水平扩展 |

### 3. OpenResty + Lua 🎯

**OpenResty：** 基于 Nginx + Lua 的高性能 Web 平台，可以在 Nginx 里直接写业务逻辑。

```nginx
location /item {
    # ① 先查 nginx 本地缓存
    local cache = ngx.shared.item_cache
    local val = cache:get(id)
    if val then
        return val
    end
    # ② 查 Redis
    local redis = require("resty.redis")
    ...
    # ③ 查 Tomcat
    local resp = http_get("/item/" .. id)
    # ④ 写入本地缓存
    cache:set(id, resp, 1800)
    return resp
}
```

**🎯 为什么用 OpenResty 而不是直接在 Java 里做？**
> 请求在**到达 Tomcat 之前**就被拦截，减少 Tomcat 压力；Lua 脚本在 Nginx 里执行，性能接近 C 语言。

### 4. 缓存同步（Canal）🎯

**问题：** 多级缓存下，数据库更新了，怎么让所有缓存同步？

**三种方案：**

| 方案 | 做法 | 优点 | 缺点 |
|---|---|---|---|
| **设置 TTL** | 过期自动失效 | 简单 | 时效性差 |
| **主动更新** | 改库时主动更新缓存 | 时效性高 | 代码侵入大 |
| **监听 binlog**（**Canal**） | 伪装成 MySQL slave 监听变更 | **无侵入** | 架构复杂 |

**Canal 原理：**
```
① Canal 伪装成 MySQL 的 slave
② 向 MySQL 发送 dump 协议请求
③ MySQL 把 binlog 推送给 Canal
④ Canal 解析 binlog，通知客户端
⑤ 客户端更新缓存
```

**🎯 面试点：为什么用 Canal 而不是应用层双写？**
> 1. **无侵入**：业务代码不用改
> 2. **可靠**：基于 MySQL binlog，不会漏
> 3. **解耦**：缓存更新逻辑独立出来

---

## 高级篇 · Redis 最佳实践

### 1. 键值设计 🎯

#### 优雅的 key 设计

| 原则 | 说明 | 反例 |
|---|---|---|
| **可读性** | 用 `:` 分隔，见名知意 | `k1`、`a` |
| **简洁性** | 不要太长（key 也占内存） | `this:is:a:very:long:key:name` |
| **不重复** | 不要包含重复前缀 | `hmdp:hmdp:user:1` |
| **有层次** | `业务:类型:id` | `hmdp:user:1` |

**🎯 面试点：key 太长会有什么问题？**
> 1. **占内存**：key 本身也要存储
> 2. **网络开销**：每次请求都要传输 key
> 3. **计算开销**：CRC16 计算变慢

#### BigKey 问题 🎯

**什么是 BigKey：**
- **Value 很大**：String > 10KB，集合元素 > 5000 个
- **Key 本身很大**：key 长度 > 1KB

**危害：**
1. **网络阻塞**：一次传输大量数据
2. **命令阻塞**：操作 BigKey 耗时长（Redis 单线程）
3. **内存不均**：集群下导致数据倾斜
4. **删除阻塞**：`DEL` 大 key 会阻塞

**发现方式：**
```bash
redis-cli --bigkeys              # 扫描统计
redis-cli --memkeys              # 按内存排序
MEMORY USAGE key                 # 查单个 key 内存
```

**解决：**
1. **拆分**：大 Hash 拆成多个小 Hash，大 List 拆成多个小 List
2. **不存大 value**：数据存数据库，缓存只存必要字段
3. **异步删除**：用 `UNLINK` 代替 `DEL`（非阻塞删除）

#### 选择合适的数据结构

| 需求 | 数据结构 |
|---|---|
| 存对象 | Hash |
| 去重 | Set |
| 排序 | ZSet |
| 队列 | List / Stream |
| 计数 | String (INCR) |
| 布尔状态 | BitMap |

### 2. 批处理优化

#### Pipeline 管道 🎯

**问题：** 10000 次 `SET`，每次都要**往返一次网络**（RTT）→ 慢。

**解决：** Pipeline 把多条命令**一次性发送**，一次性接收结果。

```java
// ❌ 逐条发送：10000 次 RTT
for (int i = 0; i < 10000; i++) {
    stringRedisTemplate.opsForValue().set("key" + i, "value" + i);
}

// ✅ Pipeline：1 次 RTT
stringRedisTemplate.executePipelined((RedisCallback<Object>) connection -> {
    for (int i = 0; i < 10000; i++) {
        connection.set(("key" + i).getBytes(), ("value" + i).getBytes());
    }
    return null;
});
```

**🎯 Pipeline vs 事务（MULTI/EXEC）：**

| | Pipeline | 事务 |
|---|---|---|
| 原子性 | ❌ 不保证 | ✅ 保证 |
| 目的 | **减少 RTT** | 保证原子性 |
| 命令失败 | 其他命令照常执行 | 其他命令照常执行（Redis 事务不支持回滚） |

**⚠️ Pipeline 的注意事项：**
1. **不要一次发送太多命令**：数据量太大会撑爆客户端缓冲区（建议分批，如每批 500 条）
2. **集群模式下要用同一个槽**（`{}` 技巧）

### 3. 服务端优化

#### 持久化配置

| 场景 | 建议 |
|---|---|
| 缓存（可丢数据） | 关闭持久化，或只用 RDB |
| 存储（不可丢） | 开启 AOF + `everysec` |

#### 慢查询

```conf
slowlog-log-slower-than 10000    # 超过 10ms 记录（单位微秒）
slowlog-max-len 128              # 最多记录 128 条
```
```bash
SLOWLOG GET 10                   # 查最近 10 条慢查询
SLOWLOG LEN                      # 慢查询数量
SLOWLOG RESET                    # 清空
```

#### 命令及安全配置

```conf
# ⚠️ 危险命令禁用/重命名
rename-command FLUSHALL ""
rename-command FLUSHDB ""
rename-command KEYS ""
rename-command CONFIG "CONFIG_xxx"

# 设置密码
requirepass 复杂密码

# 不用默认端口
port 16379
```

**🎯 面试点：为什么禁用 `KEYS`？**
> `KEYS *` 会遍历所有 key，Redis 单线程执行期间会**阻塞所有其他请求**。
> 替代方案：`SCAN cursor MATCH pattern COUNT 100`

#### 内存配置

```conf
maxmemory 2gb                          # 最大内存
maxmemory-policy allkeys-lru           # 淘汰策略
```

**八种内存淘汰策略 🎯：**

| 策略 | 说明 |
|---|---|
| `noeviction` | 不淘汰，写入报错（**默认**） |
| `volatile-lru` | 在**设置了过期时间**的 key 中，用 LRU 淘汰 |
| `volatile-lfu` | 在设置了过期时间的 key 中，用 LFU 淘汰 |
| `volatile-ttl` | 淘汰**剩余时间最短**的 |
| `volatile-random` | 随机淘汰设置了过期时间的 |
| `allkeys-lru` | 在**所有 key** 中用 LRU（**推荐用于缓存**） |
| `allkeys-lfu` | 在所有 key 中用 LFU |
| `allkeys-random` | 随机淘汰 |

**🎯 LRU vs LFU：**

| | LRU | LFU |
|---|---|---|
| 依据 | **最近最少使用**（时间） | **最不经常使用**（频率） |
| 问题 | 偶发访问会挤掉热点数据 | 需要额外计数器 |
| 适用 | 一般场景 | 热点数据明显 |

### 4. 集群还是主从 🎯

| 场景 | 选择 |
|---|---|
| 数据量小、并发不高 | **主从 + 哨兵** |
| 数据量大（> 单机内存） | **分片集群** |
| 高并发写 | **分片集群** |

**⚠️ 分片集群的代价：**
1. **不支持多 key 操作**（跨槽）
2. **Lua 脚本受限**（KEYS 必须同槽）
3. **运维复杂**
4. **客户端复杂**（要处理重定向 MOVED/ASK）

---

# 原理篇

## 原理篇 · Redis 数据结构

### 1. 动态字符串（SDS）🎯

**Redis 没有直接用 C 字符串，而是自己实现了 SDS（Simple Dynamic String）。**

```c
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len;        // 已用长度
    uint8_t alloc;      // 总分配长度
    unsigned char flags;// 类型标志（sdshdr5/8/16/32/64）
    char buf[];         // 字符数组
};
```

**🎯 SDS vs C 字符串：**

| | C 字符串 | SDS |
|---|---|---|
| 获取长度 | O(n) 遍历 | **O(1)**（有 len 字段） |
| 二进制安全 | ❌（`\0` 会截断） | ✅（按 len 读） |
| 缓冲区溢出 | ❌ 会溢出 | ✅ 自动扩容 |
| 内存分配 | 每次修改都分配 | **空间预分配 + 惰性释放** |

**🎯 为什么 SDS 有 5 种类型（sdshdr5/8/16/32/64）？**
> **节省内存**。短字符串用 `sdshdr8`（len 只占 1 字节），长字符串才用 `sdshdr32`。
> 如果用统一的 `sdshdr64`，每个 key 都浪费 7 字节。

### 2. intset（整数集合）

**用途：** 当 **Set 的元素都是整数**且**元素数量不多**时，用 intset 存储。

```c
typedef struct intset {
    uint32_t encoding;   // 编码方式：16/32/64 位
    uint32_t length;     // 元素个数
    int8_t contents[];   // 有序数组
} intset;
```

**🎯 特点：**
- **有序**（二分查找，O(logN)）
- **无重复**
- **内存紧凑**（连续数组）
- **升级机制**：加入大整数时，整个数组升级编码（如 16位 → 32位），**不支持降级**

### 3. Dict（字典）🎯

**用途：** Hash 类型的底层实现之一，也是 Redis 的**全局键空间**。

```c
typedef struct dict {
    dictType *type;
    dictEntry **ht_table[2];   // ⚠️ 两个哈希表（用于渐进式 rehash）
    unsigned long ht_used[2];
    long rehashidx;            // rehash 进度（-1 表示没在 rehash）
} dict;
```

#### 渐进式 Rehash 🎯

**问题：** 哈希表扩容时，如果一次性把所有元素迁移到新表，数据量大时会**阻塞很久**。

**解决：渐进式 rehash**
```
① 给 ht_table[1] 分配空间（通常是 ht_table[0] 的 2 倍）
② rehashidx = 0
③ 每次对 dict 操作时，顺便把 ht_table[0][rehashidx] 的元素迁移到 ht_table[1]
④ rehashidx++
⑤ 全部迁移完，释放 ht_table[0]，把 ht_table[1] 变成 ht_table[0]，rehashidx = -1
```

**⚠️ rehash 期间如何查找？** → **两张表都查**（先查 ht_table[0]，没有再查 ht_table[1]）

**🎯 面试点：为什么用渐进式 rehash？**
> 避免一次性 rehash 造成的长时间阻塞，把成本**均摊**到每次操作上。

### 4. ZipList（压缩列表）🎯

**用途：** 元素较少时，List / Hash / ZSet 的底层实现。**内存紧凑**。

```
<zlbytes> <zltail> <zllen> <entry1> <entry2> ... <entry3> <zlend>
```

每个 entry：
```
<prevlen> <encoding> <data>
```

**🎯 prevlen 的作用：** 记录**前一个节点的长度**，用于**从后往前遍历**。

**🎯 连锁更新问题：**

`prevlen` 占用 1 字节（前节点长度 < 254）或 5 字节（≥ 254）。

```
如果所有节点长度都是 250（prevlen 占 1 字节）
→ 某节点长度变成 255
→ 后一个节点的 prevlen 要扩到 5 字节
→ 这个节点总长度也变了 → 再后一个节点也要扩
→ 连锁反应！
```

**⚠️ 性能影响：** 最坏情况 O(N²)。但**实际触发概率很低**（需要连续多个节点长度接近 254）。

### 5. QuickList（快速列表）

**用途：** **List 的底层实现**（Redis 3.2+）。

**本质：** **双向链表 + ZipList** 的结合
```
QuickList
  ├── QuickListNode → ZipList（存多个元素）
  ├── QuickListNode → ZipList
  └── QuickListNode → ZipList
```

**🎯 为什么这样设计？**
> - 纯链表：每个节点有前后指针开销（16 字节）+ 内存碎片
> - 纯 ZipList：元素多了，申请连续内存困难，且连锁更新风险大
> - **QuickList：兼顾时间效率和空间效率**

### 6. SkipList（跳表）🎯

**用途：** **ZSet 的底层实现之一**（元素较多时）。

**结构：**
```
level 3:  1 ─────────────────────→ 9
level 2:  1 ──────→ 5 ──────────→ 9
level 1:  1 ──→ 3 → 5 ──→ 7 ────→ 9
level 0:  1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9
```

**🎯 跳表 vs 平衡树（红黑树）：**

| | 跳表 | 红黑树 |
|---|---|---|
| 实现复杂度 | **简单** | 复杂（旋转、变色） |
| **范围查询** | **容易**（底层是有序链表） | 需要中序遍历 |
| 插入/删除 | 只需改指针 | 需要旋转 |
| 内存 | 稍多（多级索引） | 较少 |
| 并发 | **容易实现无锁** | 难 |

**🎯 面试点：为什么 ZSet 用跳表不用红黑树？**
> 1. **范围查询更高效**：`ZRANGEBYSCORE` 需要按顺序遍历，跳表底层是有序链表，天然支持
> 2. **实现简单**，代码可维护性好
> 3. **容易做并发控制**

### 7. RedisObject 🎯

**所有 Redis 数据类型的统一封装：**

```c
typedef struct redisObject {
    unsigned type:4;        // 类型（String/List/Set/ZSet/Hash）
    unsigned encoding:4;    // 编码方式（int/embstr/raw/ziplist/...）
    unsigned lru:24;        // LRU 时间 / LFU 计数
    int refcount;           // 引用计数（用于对象共享）
    void *ptr;              // 指向实际数据的指针
} robj;
```

**🎯 encoding 决定了底层用什么数据结构：**

| 类型 | 可能的 encoding |
|---|---|
| String | `int`、`embstr`、`raw` |
| List | `quicklist` |
| Set | `intset`、`listpack`/`ziplist`、`hashtable` |
| ZSet | `ziplist`/`listpack`、`skiplist` |
| Hash | `ziplist`/`listpack`、`hashtable` |

**🎯 String 的三种编码：**

| 编码 | 条件 | 特点 |
|---|---|---|
| `int` | 值是**整数**且能用 long 表示 | 直接存整数，不存字符串 |
| `embstr` | 长度 **≤ 44 字节** | 与 RedisObject **连续分配**（一次内存分配） |
| `raw` | 长度 **> 44 字节** | 分开分配（两次内存分配） |

**⚠️ 为什么是 44 字节？** RedisObject 占 16 字节，SDS 头部 3 字节，加上结尾 `\0` 1 字节，共 20 字节；jemalloc 分配 64 字节内存块 → 64 - 20 = **44**。

### 8. 五种数据类型的底层结构 🎯

| 类型 | 小数据量 | 大数据量 |
|---|---|---|
| **String** | int / embstr | raw |
| **List** | quicklist | quicklist |
| **Set** | intset（全整数） / listpack | hashtable |
| **ZSet** | listpack | **skiplist + dict** |
| **Hash** | listpack | hashtable |

**🎯 ZSet 为什么同时用跳表和字典？**
> - **跳表**：支持按 score 范围查询、排序
> - **字典**：支持 O(1) 按成员查 score（`ZSCORE`）
> 两者配合，兼顾两种查询需求。

---

## 原理篇 · Redis 网络模型 🎯

### 1. 用户空间与内核空间

```
用户程序（Redis）
    ↕ 系统调用
内核空间（操作系统）
    ↕
硬件（网卡、磁盘）
```

**为什么要区分：** 安全隔离，用户程序不能直接操作硬件。

**⚠️ 代价：** 数据在用户空间和内核空间之间**来回拷贝**，是 IO 性能的瓶颈。

### 2. 五种 IO 模型 🎯

| 模型 | 特点 | 缺点 |
|---|---|---|
| **阻塞 IO** | 一直等数据 | 浪费 CPU |
| **非阻塞 IO** | 轮询检查 | CPU 空转 |
| **IO 多路复用** | 一个线程管多个连接 | ✅ **Redis 用的** |
| 信号驱动 IO | 内核通知 | 用得少 |
| 异步 IO（AIO） | 内核完成后再通知 | Linux 支持不完善 |

### 3. IO 多路复用详解 🎯

#### select

```c
int select(int nfds, fd_set *readfds, fd_set *writefds,
           fd_set *exceptfds, struct timeval *timeout);
```
**⚠️ 问题：**
1. **fd 数量限制**（默认 1024）
2. 每次调用都要**把 fd 集合从用户空间拷贝到内核空间**
3. 返回后要**遍历所有 fd** 找就绪的（O(n)）

#### poll

**改进：** 用链表存 fd，**没有数量限制**
**⚠️ 仍有问题：** 拷贝开销 + 遍历开销

#### epoll 🎯

```c
int epoll_create(int size);                          // 创建 epoll 实例
int epoll_ctl(int epfd, int op, int fd, ...);        // 注册 fd
int epoll_wait(int epfd, struct epoll_event *events, // 等待事件
               int maxevents, int timeout);
```

**🎯 epoll 的三大优势：**

| 优势 | 说明 |
|---|---|
| **无 fd 数量限制** | 基于红黑树存储 fd |
| **零拷贝** | `epoll_ctl` 注册一次，不用每次重复拷贝 |
| **O(1) 就绪通知** | 内核用**就绪列表**直接返回就绪的 fd，不用遍历 |

**🎯 epoll 的两个触发模式：**

| | **LT（水平触发，默认）** | **ET（边缘触发）** |
|---|---|---|
| 通知时机 | 只要缓冲区**有数据**就一直通知 | 只在**状态变化**时通知一次 |
| 编程难度 | 简单 | 复杂（必须一次读完） |
| 效率 | 稍低（重复通知） | **高** |
| 使用 | select/poll 类似 | Nginx、Redis 部分场景 |

**🧠 记忆：LT = 一直提醒你直到处理完；ET = 只提醒一次，处理不处理随你**

### 4. Redis 是单线程吗 🎯

**🎯 面试标准答案：**

> **Redis 的命令执行是单线程的，但 Redis 本身不是单线程的。**

Redis 启动时会启动多个后台线程：
- **主线程**：处理命令（核心，单线程）
- `bio_close_file`：异步关闭文件
- `bio_aof_fsync`：AOF 刷盘
- `bio_lazy_free`：惰性释放内存
- **Redis 6.0+**：额外的 **IO 线程**处理网络读写

**🎯 为什么用单线程？**

| 原因 | 说明 |
|---|---|
| **避免锁竞争** | 单线程无需加锁，没有上下文切换开销 |
| **避免线程安全问题** | 命令天然原子（这也是 Lua 脚本能保证原子性的原因） |
| **CPU 不是瓶颈** | Redis 是**内存 + IO 密集型**，瓶颈在内存和网络，不在 CPU |
| **代码简单** | 易于维护 |

**🎯 Redis 6.0 为什么引入多线程？**
> 瓶颈从 CPU 转移到了**网络 IO**。
> 多线程只用于**网络数据的读写和解析**，**命令执行仍然是单线程** → 既提升吞吐，又保持原子性。

### 5. RESP 协议 🎯

**RESP（REdis Serialization Protocol）：** Redis 的通信协议。

**5 种数据类型前缀：**

| 前缀 | 类型 | 示例 |
|---|---|---|
| `+` | 简单字符串 | `+OK\r\n` |
| `-` | 错误 | `-ERR unknown command\r\n` |
| `:` | 整数 | `:1000\r\n` |
| `$` | 批量字符串 | `$6\r\nfoobar\r\n` |
| `*` | 数组 | `*2\r\n$3\r\nfoo\r\n$3\r\nbar\r\n` |

**🎯 特点：**
- **简单**：人可读，易于实现
- **二进制安全**：`$` 后面跟长度，不依赖结束符
- **统一格式**：客户端发送的是**数组格式**的命令

---

## 原理篇 · 内存回收 🎯

### 1. 过期 key 的处理

#### 三种策略

| 策略 | 做法 | 优点 | 缺点 |
|---|---|---|---|
| **定时删除** | 设置定时器，到期立即删 | 内存及时释放 | **CPU 开销大** |
| **惰性删除** | 访问时才检查是否过期 | CPU 友好 | 内存不及时释放 |
| **定期删除** | 每隔一段时间**随机抽查** | 折中 | 需要调参 |

**Redis 用的是：惰性删除 + 定期删除**

#### 定期删除的具体实现（🎯 面试常问）

```
① 每秒执行 10 次（hz 配置，默认 10）
② 每次随机抽取 20 个设置了过期时间的 key
③ 删除其中已过期的
④ 如果过期 key 的比例超过 25%，重复步骤 ②
⑤ 单次执行时间不超过 25ms（避免阻塞）
```

**🧠 记忆：** **每秒 10 次、每次抽 20 个、超过 25% 就继续、最多 25ms**

#### ⚠️ 如果大量 key 同时过期会怎样？

**问题：** 定期删除的循环会一直执行（因为每次都超过 25%），可能导致**主线程阻塞**。

**预防：** 设置过期时间时**加随机值**，避免集中过期（这也是**缓存雪崩**的防范措施）。

### 2. 内存淘汰策略

**触发时机：** 内存达到 `maxmemory` 时

**八种策略（前面已列）：**
```
noeviction          不淘汰，报错（默认）
volatile-lru        过期 key 中，LRU
volatile-lfu        过期 key 中，LFU
volatile-ttl        过期 key 中，剩余时间最短
volatile-random     过期 key 中，随机
allkeys-lru         所有 key 中，LRU  ← 缓存场景推荐
allkeys-lfu         所有 key 中，LFU
allkeys-random      所有 key 中，随机
```

**🎯 LRU 的两种实现：**

| | 传统 LRU | Redis 的近似 LRU |
|---|---|---|
| 实现 | 双向链表 + HashMap | **随机采样** |
| 精确性 | 精确 | 近似 |
| 内存 | 需要额外链表 | **不需要额外结构** |

**Redis 近似 LRU 原理：**
```
RedisObject 里有 24 bit 的 lru 字段，记录"最后一次访问的时间戳"
淘汰时：随机采样 N 个 key（默认 5 个），淘汰其中最久未使用的
```
**🎯 为什么用近似？** 精确 LRU 需要维护链表，占用额外内存且操作复杂；近似 LRU 效果接近但成本低得多。

---

# 面试题汇总

## 一、缓存相关

### Q1：缓存穿透、击穿、雪崩的区别和解决方案？🎯

| | 穿透 | 击穿 | 雪崩 |
|---|---|---|---|
| **数据存在吗** | ❌ 不存在 | ✅ 存在 | ✅ 存在 |
| **原因** | 查询不存在的数据 | **单个热点** key 过期 | **大量** key **同时**过期 |
| **方案1** | 缓存空对象 | 互斥锁 | TTL 加随机值 |
| **方案2** | 布隆过滤器 | 逻辑过期 | Redis 集群 |
| **方案3** | 参数校验 | 热点数据永不过期 | 服务降级 |

### Q2：缓存和数据库如何保证一致性？🎯

**推荐方案：Cache Aside（先更新数据库，再删除缓存）**

**为什么是删除而不是更新？**
1. 删除更简单，不用计算新值
2. 避免并发写覆盖
3. 懒加载，用不到就不写

**并发问题与解决：**
- **延迟双删**：更新库 → 删缓存 → 延迟 → 再删
- **TTL 兜底**：最终一致
- **Canal 监听 binlog**：异步更新

### Q3：为什么不用更新缓存而是删除缓存？🎯

见上。

---

## 二、分布式锁相关

### Q4：如何用 Redis 实现分布式锁？🎯

```java
// 加锁（一条命令保证原子性）
SET lock:order:1 uuid EX 30 NX

// 解锁（Lua 脚本保证"判断+删除"原子性）
if redis.call('get', KEYS[1]) == ARGV[1] then
    return redis.call('del', KEYS[1])
end
return 0
```

**必须有线程标识**：防止误删别人的锁。

### Q5：Redis 分布式锁的三个演进阶段？🎯

| 阶段 | 问题 | 解决 |
|---|---|---|
| 1. `SETNX` + `EXPIRE` | 两步不原子，可能死锁 | 用 `SET ... EX ... NX` 一条命令 |
| 2. 加线程标识判断 | 判断和删除不原子 | 用 **Lua 脚本** |
| 3. 锁超时释放 | 业务没执行完锁就没了 | **Redisson 看门狗**自动续期 |

### Q6：Redisson 看门狗机制？🎯

- **触发条件**：`tryLock()` **不指定** `leaseTime`
- **原理**：启动定时任务，每隔 `leaseTime/3`（默认 10s）重置锁的过期时间为 30s
- **停止**：主动 `unlock()` 或服务宕机
- **⚠️ 注意**：一旦指定 `leaseTime`，看门狗**失效**

### Q7：Redisson 可重入锁原理？🎯

用 **Hash** 存：
```
HSET lock:order:1 {线程标识} {重入次数}
```
- 加锁：`HINCRBY +1`
- 解锁：`HINCRBY -1`，减到 0 才删 key

### Q8：Redis 分布式锁 vs ZooKeeper 分布式锁？🎯

| | Redis | ZooKeeper |
|---|---|---|
| 性能 | **高** | 较低 |
| 可靠性 | 较低（主从切换可能丢锁） | **高**（CP 模型） |
| 实现 | 简单 | 复杂 |
| 适用 | 高并发、允许极端情况出错 | 强一致要求 |

---

## 三、秒杀相关

### Q9：如何解决超卖问题？🎯

**乐观锁（CAS）**：
```sql
UPDATE tb_seckill_voucher
SET stock = stock - 1
WHERE voucher_id = ? AND stock > 0
```

**为什么不用版本号？** 并发下成功率低（100 个线程只有 1 个成功）。
**用 `stock > 0`**：只要库存够就能成功，成功率高。

### Q10：如何实现一人一单？🎯

**单机**：`synchronized (userId.toString().intern())`
> ⚠️ `.intern()` 必须加！否则每次 `toString()` 都是新对象，锁不住。

**集群**：分布式锁（Redisson）

**Redis 方案**：用 Set 记录已购买用户
```lua
if redis.call('sismember', orderKey, userId) == 1 then return 2 end
redis.call('sadd', orderKey, userId)
```

### Q11：Lua 脚本为什么能保证原子性？🎯

> Redis 是**单线程**执行命令的。
> 执行 Lua 脚本时，会把**整个脚本当作一个命令**执行，中间不会插入其他命令。

### Q12：异步秒杀怎么优化？🎯

```
① Lua 脚本在 Redis 里原子判断：库存 + 一人一单
② 通过 → 立即返回"抢购成功"+ 订单号
③ 订单丢进消息队列（List / Stream）
④ 后台线程消费队列，慢慢落库
```

**提升**：响应时间从"一次数据库事务"降到"一次 Redis 调用"

### Q13：为什么用 Redis Stream 不用 List 做消息队列？🎯

| | List | Stream |
|---|---|---|
| 一对一 | ✅ | ✅ |
| **一对多（消费组）** | ❌ | ✅ |
| **消息确认（ACK）** | ❌ | ✅ |
| **消息回溯** | ❌ | ✅ |
| 消息持久化 | ✅ | ✅ |

**Stream 有消费组 + ACK 机制**，能保证消息不丢失，适合订单场景。

---

## 四、数据结构相关

### Q14：ZSet 底层为什么用跳表不用红黑树？🎯

1. **范围查询高效**：跳表底层是有序链表，`ZRANGEBYSCORE` 直接遍历
2. **实现简单**：不用旋转、变色
3. **容易做并发控制**

### Q15：String 的三种编码？🎯

| 编码 | 条件 |
|---|---|
| `int` | 整数值 |
| `embstr` | 长度 ≤ 44 字节 |
| `raw` | 长度 > 44 字节 |

### Q16：为什么是 44 字节？🎯

> RedisObject 16 字节 + SDS 头 3 字节 + 结尾 `\0` 1 字节 = 20 字节
> jemalloc 按 64 字节分配内存块 → 64 - 20 = **44 字节**

### Q17：跳表的层数怎么决定的？🎯

> 每次插入时**随机**决定层数（类似抛硬币），有 25% 概率增加一层。
> 期望层数是 O(logN)。

---

## 五、持久化相关

### Q18：RDB 和 AOF 的区别？🎯

| | RDB | AOF |
|---|---|---|
| 内容 | 数据快照 | 写命令日志 |
| 文件大小 | 小 | 大 |
| 恢复速度 | **快** | 慢 |
| 数据安全性 | 差（可能丢数据） | **好**（最多丢1秒） |
| 性能影响 | fork 阻塞 | 刷盘开销 |

**生产建议**：**都开**，用混合持久化。

### Q19：RDB 的 fork 会阻塞吗？🎯

> 会，但只阻塞**页表复制**的时间。
> 用的是 **写时复制（COW）**，不复制物理内存。

### Q20：AOF 的刷盘策略？🎯

| 策略 | 说明 | 安全性 |
|---|---|---|
| `always` | 每条命令都刷 | 最高，最慢 |
| `everysec` | 每秒刷一次（**默认**） | 最多丢 1 秒 |
| `no` | 交给操作系统 | 最快，可能丢很多 |

---

## 六、集群相关

### Q21：Redis 主从全量同步的过程？🎯

```
① slave 发 replid + offset
② master 判断 replid 不一致 → 全量同步
③ master bgsave 生成 RDB
④ 发送 RDB 给 slave
⑤ master 把期间的命令写入 repl_baklog，再发给 slave
```

### Q22：什么是 repl_baklog？🎯

> 一个**固定大小的环形数组**，master 记录所有写命令。
> slave 断线重连时从这里找差异做**增量同步**。
> 如果 offset 被覆盖 → 退化为**全量同步**。

### Q23：哨兵的主观下线和客观下线？🎯

| | 说明 |
|---|---|
| **主观下线 SDOWN** | **单个**哨兵认为节点挂了 |
| **客观下线 ODOWN** | **超过 quorum** 个哨兵都认为挂了 |

**为什么需要客观下线**：避免网络抖动导致误判。

### Q24：哨兵选新 master 的顺序？🎯

**断开时间 → 优先级（小优先） → offset（大优先） → runid（小优先）**

### Q25：Redis 集群有多少个槽？为什么？🎯

**16384 个**

原因：
1. 16384 个槽的**位图只占 2KB**（65536 要 8KB），心跳包更小
2. 集群节点数一般不超过 1000，16384 足够分配

### Q26：什么是 CROSSSLOT 错误？怎么解决？🎯

**错误**：Lua 脚本 / 事务中的多个 key 不在同一个槽。
**解决**：用 `{}` 让 key 落在同一个槽
```
{user}:1
{user}:2    ← 都按 "user" 算槽 → 同一个节点
```

---

## 七、原理相关

### Q27：Redis 是单线程吗？🎯

> **命令执行是单线程，但 Redis 本身不是单线程。**
> 还有后台线程处理 AOF 刷盘、惰性删除等；Redis 6.0+ 还有 IO 线程处理网络读写。

### Q28：为什么用单线程？🎯

1. **避免锁竞争和上下文切换**
2. **保证命令原子性**（这也是 Lua 脚本能保证原子性的基础）
3. **CPU 不是瓶颈**（内存 + IO 密集型）
4. 代码简单易维护

### Q29：Redis 6.0 为什么引入多线程？🎯

> 瓶颈从 CPU 转到**网络 IO**。
> 多线程只用于**网络读写和解析**，**命令执行仍是单线程**。

### Q30：epoll 比 select 好在哪？🎯

| | select | epoll |
|---|---|---|
| fd 限制 | 1024 | **无限制** |
| 拷贝 | 每次调用都拷贝 fd 集合 | **只注册一次** |
| 查找就绪 | 遍历所有 fd，O(n) | **就绪列表，O(1)** |

### Q31：Redis 的过期 key 怎么处理？🎯

**惰性删除 + 定期删除**

定期删除：每秒 10 次，每次随机抽 20 个，过期比例 > 25% 就继续，单次不超过 25ms。

### Q32：什么是 BigKey？有什么危害？🎯

**定义**：Value > 10KB 的 String，或元素 > 5000 的集合。

**危害**：
1. 网络阻塞
2. 命令阻塞（Redis 单线程）
3. 内存不均（集群数据倾斜）
4. `DEL` 阻塞

**解决**：拆分、不存大 value、用 `UNLINK` 异步删除。

### Q33：Pipeline 和事务的区别？🎯

| | Pipeline | 事务（MULTI/EXEC） |
|---|---|---|
| 目的 | **减少 RTT** | 保证原子性 |
| 原子性 | ❌ | ✅（但**不支持回滚**） |

**⚠️ Redis 事务不支持回滚**：如果某条命令失败，其他命令**照常执行**。

### Q34：为什么禁用 KEYS 命令？🎯

> `KEYS *` 遍历所有 key，Redis 单线程执行期间**阻塞所有其他请求**。
> 替代：`SCAN cursor MATCH pattern COUNT 100`（游标分批，不阻塞）

---

# 知识图谱

## Redis 全景图

```
Redis
├─ 基础篇
│   ├─ 认识 ─┬─ NoSQL 四大类（键值/文档/列族/图形）
│   │        ├─ 八大特点（内存/单线程/持久化/集群/Lua...）
│   │        └─ vs Memcached
│   ├─ 安装 ─┬─ 三种启动方式
│   │        └─ ⚠️ bind 配置（踩坑：Connection refused）
│   ├─ 命令 ─┬─ 通用（KEYS/DEL/EXPIRE/TTL/SCAN）
│   │        ├─ String ── INCR（原子）/ SETNX / SET EX NX
│   │        ├─ Hash ──── HSET/HGETALL（存对象）
│   │        ├─ List ──── LPUSH/RPOP（栈/队列）
│   │        ├─ Set ───── SADD/SINTER（去重/共同好友/一人一单）
│   │        ├─ ZSet ──── ZADD/ZREVRANGE（排行榜）
│   │        └─ Key 层级格式（业务:类型:id）
│   └─ Java 客户端
│       ├─ Jedis ──── 线程不安全，需连接池
│       ├─ Lettuce ── 线程安全（默认）
│       ├─ Redisson ─ 分布式工具
│       └─ Spring Data Redis
│           ├─ RedisTemplate ── 默认 JDK 序列化（乱码）
│           ├─ RedisSerializer ─ 4 种对比
│           └─ StringRedisTemplate ── 手动 JSON（推荐）
│
├─ 实战篇（黑马点评）
│   ├─ 短信登录
│   │   ├─ Session 方案 ──── 集群下不共享
│   │   ├─ Redis 方案 ────── 无状态、可扩展
│   │   ├─ 两级拦截器 ────── RefreshToken + Login
│   │   ├─ UserHolder ────── ThreadLocal 存 UserDTO
│   │   └─ ⚠️ 踩坑 ──────── 两个 ThreadLocal 搞混 / 放行路径少斜杠 / 错误被伪装成 401
│   ├─ 商户查询缓存
│   │   ├─ 更新策略 ──── 内存淘汰 / 超时剔除 / 主动更新
│   │   ├─ Cache Aside ─ 先更新库，再删缓存
│   │   ├─ 缓存穿透 ──── 缓存空对象 / 布隆过滤器
│   │   ├─ 缓存雪崩 ──── TTL 随机值 / 集群
│   │   ├─ 缓存击穿 ─┬─ 互斥锁（强一致，有等待）
│   │   │             └─ 逻辑过期（最终一致，无等待）
│   │   └─ CacheClient ─ 封装工具类（函数式接口）
│   ├─ 优惠券秒杀
│   │   ├─ 全局唯一 ID ─── Redis INCR + 时间戳
│   │   ├─ 超卖 ────────── 乐观锁（stock > 0）
│   │   ├─ 一人一单 ────── synchronized + intern() / Lua + Set
│   │   └─ 异步秒杀 ────── Lua 判断 + 消息队列 + 后台线程
│   ├─ 分布式锁
│   │   ├─ 方案对比 ────── MySQL / Redis / ZK
│   │   ├─ 实现演进 ─┬─ SETNX+EXPIRE（不原子）
│   │   │             ├─ 加线程标识（防误删）
│   │   │             ├─ Lua 脚本（保证原子）
│   │   │             └─ Redisson（看门狗续期）
│   │   └─ Redisson ─┬─ 可重入（Hash + 计数）
│   │                 ├─ 锁重试（Pub/Sub）
│   │                 ├─ WatchDog（自动续期）
│   │                 └─ MultiLock（联锁）
│   ├─ Redis 消息队列
│   │   ├─ List ──── 不支持一对多
│   │   ├─ PubSub ── 不持久化
│   │   └─ Stream ─┬─ 消费组（XGROUP）
│   │               ├─ 确认机制（XACK）
│   │               └─ ⚠️ 必须先创建消费组，否则 NOGROUP
│   ├─ 达人探店
│   │   ├─ 点赞 ──── Set（去重）
│   │   └─ 排行榜 ── ZSet（score = 时间戳）
│   ├─ 好友关注
│   │   ├─ 共同关注 ── Set 交集（SINTER）
│   │   └─ Feed 流 ─┬─ 推模式（写扩散，读快）
│   │                ├─ 拉模式（读扩散，写快）
│   │                └─ 滚动分页（ZREVRANGEBYSCORE + offset）
│   ├─ 附近商铺 ────── GEO（底层 ZSet + GeoHash）
│   ├─ 用户签到 ────── BitMap（SETBIT / BITFIELD）
│   └─ UV 统计 ─────── HyperLogLog（12KB 统计 2^64）
│
├─ 高级篇
│   ├─ 分布式缓存
│   │   ├─ 持久化 ─┬─ RDB（快照 + fork + COW）
│   │   │           ├─ AOF（命令日志 + 三种刷盘）
│   │   │           └─ 混合持久化（推荐）
│   │   ├─ 主从 ───┬─ 全量同步（replid + RDB）
│   │   │           └─ 增量同步（repl_baklog 环形数组）
│   │   ├─ 哨兵 ───┬─ 监控 / 故障恢复 / 通知
│   │   │           ├─ 主观下线 vs 客观下线
│   │   │           └─ 选主顺序（断开时间→优先级→offset→runid）
│   │   └─ 分片集群 ┬─ 16384 个散列插槽
│   │                ├─ {} 强制同槽
│   │                └─ 自动故障转移
│   ├─ 多级缓存
│   │   ├─ JVM 进程缓存（Caffeine）
│   │   ├─ Nginx 本地缓存（OpenResty + Lua）
│   │   ├─ Redis 缓存
│   │   └─ 缓存同步（Canal 监听 binlog）
│   └─ 最佳实践
│       ├─ 键值设计 ── 优雅 key / BigKey 问题
│       ├─ 批处理 ──── Pipeline / mset
│       ├─ 服务端 ──── 持久化 / 慢查询 / 危险命令禁用
│       └─ 内存 ────── 8 种淘汰策略（LRU/LFU）
│
└─ 原理篇
    ├─ 数据结构
    │   ├─ SDS ──────── 动态字符串（O(1) 长度 + 二进制安全）
    │   ├─ intset ───── 整数集合（有序 + 升级）
    │   ├─ Dict ─────── 字典（渐进式 rehash 双表）
    │   ├─ ZipList ──── 压缩列表（连锁更新）
    │   ├─ QuickList ── 双向链表 + ZipList
    │   ├─ SkipList ─── 跳表（ZSet 底层）
    │   └─ RedisObject ┬─ type / encoding / lru / refcount
    │                   └─ String 三编码（int/embstr/raw，44字节）
    ├─ 网络模型
    │   ├─ 五种 IO 模型（阻塞/非阻塞/多路复用/信号驱动/异步）
    │   ├─ select / poll / epoll 对比
    │   ├─ epoll 的 LT 和 ET
    │   ├─ 单线程 vs 多线程（6.0 引入 IO 线程）
    │   └─ RESP 协议（5 种前缀：+ - : $ *）
    └─ 内存回收
        ├─ 过期策略 ── 惰性 + 定期（每秒10次，抽20个，>25%继续）
        └─ 淘汰策略 ── 8 种（allkeys-lru 推荐）
```

## 本项目 Redis key 一览

| key 前缀 | 类型 | 用途 | TTL |
|---|---|---|---|
| `login:code:` | String | 登录验证码 | 2 分钟 |
| `login:token:` | Hash | 登录会话 | 1 小时 |
| `logout:user:` | String | 用户当前 token（用于登出） | 1 小时 |
| `cache:shop:` | String | 商铺缓存 | 30 分钟 |
| `cache:shopType:` | String | 商铺类型缓存 | 30 分钟 |
| `lock:shop:` | String | 缓存重建锁 | 10 秒 |
| `lock:order:` | RLock | 下单分布式锁（Redisson） | 看门狗 |
| `seckill:stock:` | String | 秒杀库存 | 无 |
| `seckill:order:` | Set | 已下单用户 | 无 |
| `stream.orders` | Stream | 订单消息队列 | 无 |
| `icr:order:` | String | 订单号自增序列 | 按天 |
| `blog:liked:` | ZSet | 博客点赞（按时间排序） | 无 |
| `follow:` | Set | 用户关注列表 | 无 |
| `feed:` | ZSet | Feed 流收件箱 | 无 |
| `shop:geo:` | GEO | 商铺地理位置 | 无 |
| `sign:` | BitMap | 用户签到 | 无 |

## 踩坑记录汇总（本项目真实遇到）

| # | 现象 | 根因 | 解决 |
|---|---|---|---|
| 1 | Redis `Connection refused` | 配置了局域网 IP，但 Redis 只绑 `127.0.0.1` | host 改 `localhost` |
| 2 | `expire` 抛 `StackOverflowError` | `redisson-spring-data-32` 与 Spring Data Redis 3.5 不兼容 | 换 `redisson` 核心包 |
| 3 | 登录时报 NPE（`fieldValue` is null） | `setFieldValueEditor` 无条件 `.toString()` | 加 null 判断 |
| 4 | `UserHolder.getUser()` 永远 null | 两个 ThreadLocal 用混了 | 统一成一个 |
| 5 | `/blog/hot` 无 token 却 401 | 放行路径少了开头的 `/` | 改成 `"/blog/hot"` |
| 6 | 报错变成 401 且 body 为空 | `/error` 被登录拦截器拦截 | 放行 `/error` |
| 7 | `unlock.lua` 加载失败 | 文件在 `mapper/` 下，代码找根目录 | 路径改成 `mapper/unlock.lua` |
| 8 | 日志疯狂刷屏（NOGROUP） | `XADD` 不创建消费组，消费者却用 `g1` 读 | `init()` 里先 `createGroup` |
| 9 | 异常类型看不到 | `log.error` 没传异常对象 | 改成 `log.error("...", e)` |
| 10 | 后台线程 `Cannot find current proxy` | `AopContext` 依赖 ThreadLocal，后台线程没有 | 用 `@Resource @Lazy` 自身注入代理 |
| 11 | JMeter 401（配置了 token 却没用） | 变量写法错（`${token}` 未定义）/ CSV 编码带 Tab | 用 `${token}` + CSV「首行是表头」勾选 |

---

# 附录 A · 难代码逐行精讲

> 这一章挑出笔记里**最难懂**、最容易看不懂就跳过、但面试又最爱问的几段代码，逐行拆解。

## A1 · Redisson 可重入锁的 Lua 脚本（最难）🎯

### 为什么难

1. **Lua 语法 + Redis 命令 + 并发语义**三重叠加
2. 用 **Hash 结构**同时存了"锁的归属"和"重入次数"两个信息
3. 加锁和解锁的返回值有**三态**（nil / 0 / 1），业务代码要分别处理

### 加锁脚本逐行解析

```lua
-- KEYS[1] = 锁名（如 lock:order:1）
-- ARGV[1] = 锁的过期时间（毫秒，默认 30000）
-- ARGV[2] = 线程标识（UUID + ":" + threadId）
-- 返回值：nil = 加锁成功；数字 = 加锁失败，返回锁的剩余存活时间（毫秒）

if (redis.call('exists', KEYS[1]) == 0) then
    -- ① 锁不存在 → 我是第一个来的
    redis.call('hincrby', KEYS[1], ARGV[2], 1)   -- 建 Hash，field=线程标识，count=1
    redis.call('pexpire', KEYS[1], ARGV[1])       -- 设置过期时间
    return nil                                    -- ★ 返回 nil 表示成功
end

if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then
    -- ② 锁存在，且 Hash 里有我的线程标识 → 重入
    redis.call('hincrby', KEYS[1], ARGV[2], 1)   -- 计数 +1
    redis.call('pexpire', KEYS[1], ARGV[1])       -- 重新计时（防止执行中过期）
    return nil                                    -- ★ 仍返回 nil 表示成功
end

-- ③ 锁存在，但不是我的 → 加锁失败
return redis.call('pttl', KEYS[1])                -- ★ 返回剩余毫秒数，供调用方决定等多久
```

**🎯 三个关键设计点：**

| 设计 | 为什么这么做 |
|---|---|
| 用 **Hash** 而不是 String | String 只能存一个值，无法同时表达"谁的锁"和"重入几次" |
| 重入时**重置过期时间** | 重入相当于又用了一次锁，时钟应该重新计算 |
| 失败时返回 **pttl** | 调用方可以**精确知道**该等多久，避免盲目轮询 |

### 解锁脚本逐行解析

```lua
-- KEYS[1] = 锁名
-- ARGV[1] = 锁的过期时间
-- ARGV[2] = 线程标识（UUID + ":" + threadId）
-- 返回 1 = 完全释放（删除 key）；返回 0 = 还有重入，仅续期；返回 nil = 不是自己的锁

if (redis.call('hexists', KEYS[1], ARGV[2]) == 0) then
    -- ① Hash 里没有我的线程标识 → 这把锁不是我的
    return nil
end

local counter = redis.call('hincrby', KEYS[1], ARGV[2], -1)   -- ② 重入计数 -1

if (counter > 0) then
    -- ③ 计数仍 > 0 → 说明还在外层方法里持有，不能真删
    redis.call('pexpire', KEYS[1], ARGV[1])                   -- 只是续期
    return 0
else
    -- ④ 计数减到 0 → 最外层调用结束，真正释放
    redis.call('del', KEYS[1])
    return 1
end
```

**🎯 重入的完整场景演示：**

```java
lock.lock();          // count: 无 → 1，返回 nil（成功）
try {
    lock.lock();      // count: 1 → 2，返回 nil（成功，重入）
    try {
        // 业务
    } finally {
        lock.unlock();   // count: 2 → 1，返回 0，【不删 key】
    }
} finally {
    lock.unlock();       // count: 1 → 0，返回 1，【删 key，真正释放】
}
```

**🧠 记忆：加锁 +1，解锁 -1，减到 0 才删 key。**

### Java 侧怎么判断"加锁成功"

```java
// RedissonLock 的关键逻辑
Long ttl = this.tryAcquireAsync(...);
return ttl == null;      // ★ ttl 为 null 才算成功，非 null 是"锁的剩余时间"
```

**⚠️ 为什么用 `ttl == null` 而不是 `ttl > 0`？**
> 因为 Lua 里 `return nil` 到 Java 侧就是 `null`，而不是 0。
> 如果写成 `ttl > 0` 会 **NullPointerException**。

### ⚠️ 面试常见追问

**Q：如果获取锁的线程宕机了怎么办？**
> 锁有**过期时间**（默认 30s），宕机后到期自动释放，不会死锁。

**Q：那业务执行超过 30s 呢？**
> 看门狗机制自动续期（见 A2）。

**Q：为什么不用 String 存锁 + 单独的计数器？**
> 因为**两条命令不是原子的**。用 Hash 一条 `HINCRBY` 就同时完成"判断归属 + 计数"，天然原子。

---

## A2 · Redisson 看门狗（WatchDog）源码级解析 🎯

### 为什么难

- 涉及**异步编程**（Netty Future）
- 「什么时候触发」的条件很隐蔽（**取决于你有没有传 leaseTime**）
- 「什么时候停」也需要理解

### 触发条件（最容易搞错）

```java
lock.tryLock();                                    // ① 不等待，leaseTime = -1
lock.tryLock(10, TimeUnit.SECONDS);                // ② 最多等 10s，leaseTime = -1
lock.tryLock(10, 30, TimeUnit.SECONDS);            // ③ 最多等 10s，leaseTime = 30s
```

**🎯 只有 leaseTime 为 -1（即不指定）时，看门狗才启动！**

```java
private <T> RFuture<Long> tryAcquireAsync(long waitTime, long leaseTime, TimeUnit unit, long threadId) {
    RFuture<Long> ttlRemainingFuture;
    if (leaseTime > 0) {
        // ★ 指定了 leaseTime → 直接按这个时间设置，【不启动看门狗】
        ttlRemainingFuture = tryLockInnerAsync(leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
    } else {
        // ★ 没指定 → 传入 internalLockLeaseTime（默认 30s），并【启动看门狗】
        ttlRemainingFuture = tryLockInnerAsync(
                commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(),
                TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG);
    }

    // 异步回调：加锁成功后才调度续期
    ttlRemainingFuture.onComplete((ttlRemaining, e) -> {
        if (e != null) {
            return;
        }
        if (ttlRemaining == null) {                  // ★ null = 加锁成功
            scheduleExpirationRenewal(threadId);     // 启动续期任务
        }
    });
    return ttlRemainingFuture;
}
```

### 续期任务的实现

```java
private void renewExpiration() {
    ExpirationEntry ee = EXPIRATION_RENEWAL_MAP.get(getEntryName());
    if (ee == null) {
        return;
    }

    // ★ 延迟 = 看门狗时间 / 3，默认 30000 / 3 = 10000ms = 10s
    Timeout task = commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
        @Override
        public void run(Timeout timeout) throws Exception {
            ExpirationEntry ent = EXPIRATION_RENEWAL_MAP.get(getEntryName());
            if (ent == null) return;
            Long threadId = ent.getFirstThreadId();
            if (threadId == null) return;

            // 异步把锁的过期时间重置为 30s
            RFuture<Boolean> future = renewExpirationAsync(threadId);
            future.onComplete((res, e) -> {
                if (e != null) {
                    log.error("Can't update lock " + getRawName() + " expiration", e);
                    EXPIRATION_RENEWAL_MAP.remove(getEntryName());
                    return;
                }
                if (res) {
                    // ★ 续期成功 → 递归调度下一次（形成无限循环）
                    renewExpiration();
                }
                // res == false 说明锁已不是自己的（被别人抢走）→ 不再续期
            });
        }
    }, internalLockLeaseTime / 3, TimeUnit.MILLISECONDS);

    ee.setTimeout(task);
}
```

**续期用的 Lua 脚本：**
```lua
if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then
    redis.call('pexpire', KEYS[1], ARGV[1]);   -- 重置为 30s
    return 1;
end;
return 0;                                       -- 锁不是自己的了
```

### 完整时间线（默认配置）

```
t=0s    加锁成功，锁的 TTL = 30s，启动看门狗
t=10s   看门狗触发 → 续期 → TTL 重置为 30s
t=20s   看门狗触发 → 续期 → TTL 重置为 30s
t=30s   （原 TTL 到期时间，但已被续期，所以锁还在）
t=35s   业务执行完 → unlock() → 取消看门狗任务 → 删除锁 key
```

### ⚠️ 三个必须记住的点

| 点 | 说明 |
|---|---|
| **① 指定 leaseTime 会关闭看门狗** | `tryLock(10, 30, SECONDS)` → 30s 后锁必定释放，不管业务有没有跑完 |
| **② 看门狗是 Netty 定时任务** | 不是 Java 的 `ScheduledExecutorService`，而是 Redisson 自己的 `Timeout` |
| **③ 服务宕机看门狗就停** | 锁会在 30s 后自动过期 → 不会死锁 |

### 🎯 面试话术

> Redisson 的看门狗机制是为了解决"业务执行时间超过锁过期时间导致锁提前释放"的问题。
> 当 `tryLock` 不指定 `leaseTime` 时，Redisson 会用默认的 30 秒作为锁过期时间，
> 同时启动一个定时任务，每隔 `leaseTime / 3`（即 10 秒）检查一次，
> 如果锁仍被当前线程持有，就把过期时间重置为 30 秒，如此循环，直到主动 `unlock` 或服务宕机。
> 注意一旦显式指定了 `leaseTime`，看门狗就不会启动，锁到点必定释放。

---

## A3 · Stream 消费者组的正确写法 🎯

### 为什么难

1. **Stream 的命令比 List 复杂得多**（XADD / XREADGROUP / XACK / XPENDING / XCLAIM）
2. **消费组必须先创建**，否则报 `NOGROUP`（本项目踩过，导致日志疯狂刷屏）
3. `ReadOffset.lastConsumed()` 和 `ReadOffset.from("0")` 的**语义完全不同**

### ① 启动时创建消费组（最容易漏的一步）

```java
private static final String queueName = "stream.orders";

@PostConstruct
public void init() {
    // ★ 必须先建组，否则消费者 read 会抛 NOGROUP
    try {
        stringRedisTemplate.opsForStream()
                .createGroup(queueName, ReadOffset.from("0"), "g1");
        // 等价于：XGROUP CREATE stream.orders g1 0 MKSTREAM
        // 好处：如果 stream 不存在，会顺便创建出来（MKSTREAM）
    } catch (Exception e) {
        // 组已经存在时会抛 BUSYGROUP 错误，这是正常情况
        log.info("消费组 g1 已存在，跳过创建：{}", e.toString());   // ★ 用 toString() 才能看到 BUSYGROUP
    }
    // ★ 组建好之后才启动消费者
    SECKILL_ORDEr_EXECUTOR.submit(new VoucherOrderHandler());
}
```

**⚠️ 三个易错点：**

| 易错点 | 后果 |
|---|---|
| 忘了建组 | `NOGROUP No such key ... or consumer group 'g1'` |
| 建组写在 `submit` 之后 | 消费者可能先跑，撞上 NOGROUP |
| 不 catch `BUSYGROUP` | 应用重启时报错（其实组已存在，是正常的） |

**⚠️ 本项目真实踩坑：`e.getMessage()` 拿不到详细信息**

```java
// 项目原来的写法（信息不全）
log.info("消费组 g1 已存在，跳过创建：{}", e.getMessage());
// 输出：消费组 g1 已存在，跳过创建：Error in execution    ← 看不到 BUSYGROUP

// ✅ 推荐：用 toString() 或直接传异常对象
log.info("消费组 g1 已存在，跳过创建：{}", e.toString());
// 输出：... RedisSystemException: Error in execution; nested exception is
//      io.lettuce.core.RedisCommandExecutionException: BUSYGROUP Consumer Group name already exists
```

**🧠 记忆：`e.getMessage()` 常常只有一层，`e.toString()` 能带上嵌套的根异常。**

### ② 消费者循环

```java
private class VoucherOrderHandler implements Runnable {
    @Override
    public void run() {
        while (true) {
            try {
                // ① 从消费者组读一条消息
                List<MapRecord<String, Object, Object>> list =
                        stringRedisTemplate.opsForStream().read(
                                Consumer.from("g1", "c1"),          // 消费组 g1，消费者 c1
                                StreamReadOptions.empty()
                                        .count(1)                    // 每次读 1 条
                                        .block(Duration.ofSeconds(2)),// 阻塞最多 2 秒（无消息时不空转）
                                StreamOffset.create(queueName,
                                        ReadOffset.lastConsumed())  // ★ 语义：只读新消息
                        );

                // ② 没读到 → 继续下一轮（block 已经等了 2 秒，不会忙等）
                if (list == null || list.isEmpty()) {
                    continue;
                }

                // ③ 解析消息
                MapRecord<String, Object, Object> entries = list.get(0);
                Map<Object, Object> value = entries.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(
                        value, new VoucherOrder(), true);

                // ④ 处理业务（加锁 + 落库）
                handleVoucherOrder(voucherOrder);

                // ⑤ ★ 确认消息（ACK）—— 【必须放在业务成功之后】
                stringRedisTemplate.opsForStream()
                        .acknowledge(queueName, "g1", entries.getId());

            } catch (Exception e) {
                log.error("订单处理线程发生异常，转入处理挂起订单", e);   // ★ 带上 e
                handlePendingList();
            }
        }
    }
}
```

**🎯 关键点逐个拆解：**

| 代码 | 含义 | 写错的后果 |
|---|---|---|
| `Consumer.from("g1", "c1")` | 组名 g1，消费者名 c1 | 组不存在 → NOGROUP |
| `.count(1)` | 一次最多读 1 条 | 读太多不好控制顺序 |
| `.block(Duration.ofSeconds(2))` | 无消息时阻塞 2 秒 | 不加会**空转烧 CPU** |
| `ReadOffset.lastConsumed()` | 等价于 `>`，**只读从未投递给本组的新消息** | 用 `from("0")` 会**重复消费历史消息** |
| `acknowledge(...)` | 确认已处理 | 不 ACK → 消息永远留在 pending |

**⚠️ `ReadOffset.lastConsumed()` vs `from("0")` 的区别（面试常问）：**

| 偏移量 | 等价命令 | 语义 | 用途 |
|---|---|---|---|
| `ReadOffset.lastConsumed()` | `>` | 只读**新**消息 | **正常消费循环** |
| `ReadOffset.from("0")` | `0` | 从头读**所有**消息 | 处理积压 / 重放 |
| `ReadOffset.from(id)` | 指定 ID | 从指定消息之后读 | 断点续传 |

### ③ 处理 pending（未确认）消息

**为什么需要：** 消费者读了消息但**没来得及 ACK 就挂了**，消息会留在 pending 列表里。重启后要把它捞出来重处理。

```java
public void handlePendingList() {
    while (true) {
        try {
            List<MapRecord<String, Object, Object>> list =
                    stringRedisTemplate.opsForStream().read(
                            Consumer.from("g1", "c1"),
                            StreamReadOptions.empty().count(1),
                            StreamOffset.create(queueName, ReadOffset.from("0"))   // ★ 从头读
                    );

            // ★ 没有 pending 消息了 → 退出循环（这是唯一的出口！）
            if (list == null || list.isEmpty()) {
                break;
            }

            MapRecord<String, Object, Object> entries = list.get(0);
            Map<Object, Object> value = entries.getValue();
            VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(
                    value, new VoucherOrder(), true);
            handleVoucherOrder(voucherOrder);
            stringRedisTemplate.opsForStream()
                    .acknowledge(queueName, "g1", entries.getId());

        } catch (Exception e) {
            log.error("处理挂起订单列表发生异常", e);      // ★ 必须带上 e
            try {
                Thread.sleep(20);
            } catch (InterruptedException ex) {
                Thread.currentThread().interrupt();        // ★ 恢复中断标志
                break;                                      // ★ 中断就退出
            }
        }
    }
}
```

**⚠️ 本项目真实踩坑（最严重的一次）：**

```java
// ❌ 项目原来的写法：没有出口，异常时无限循环
public void handlePendingList() {
    while (true) {
        try {
            read(...);          // 消费组不存在 → 每次必抛 NOGROUP
        } catch (Exception e) {
            log.error("处理挂起订单列表发生异常");   // 没传 e，看不到 NOGROUP
            Thread.sleep(20);                        // 睡 20ms 继续 → 死循环
        }
    }
}
```

**后果：** 5 秒刷 **3600 行**日志，后台线程**永远卡死**，订单全部不落库，CPU 空转。

**修复要点：**
1. **先建消费组**（治本，异常根本不会发生）
2. `log.error` **必须带异常对象 e**
3. `handlePendingList()` 的 `while(true)` **必须有 `break` 出口**（读不到就退出）

**🧠 记忆：`while(true)` + catch + sleep + 不 break = 日志炸弹。**

---

## A4 · RedisIdWorker 雪花 ID 位运算 🎯

### 完整代码

```java
@Component
public class RedisIdWorker {

    /**
     * 起始时间戳（秒）—— 2026-01-01 00:00:00
     * 作用是【缩短时间戳长度】：用"当前秒 - 起始秒"，31 位够用 69 年
     */
    private static final long EPOCH = 1767196800L;

    /** 序列号占用的位数 */
    private static final int BIT_SIZE = 32;

    private StringRedisTemplate stringRedisTemplate;

    public RedisIdWorker(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    private final String timePattern = "yyyy:MM:dd";

    public long nextId(String keyPrefix) {
        // ① 时间戳（秒）：当前时间 - 起始时间
        LocalDateTime now = LocalDateTime.now();
        long timestamp = now.toEpochSecond(ZoneOffset.of("+8")) - EPOCH;

        // ② 序列号：用 Redis INCR 保证同一天内自增，key 带日期 → 每天从 0 开始
        String nowDate = now.format(DateTimeFormatter.ofPattern(timePattern));
        Long increment = stringRedisTemplate.opsForValue()
                .increment("icr:" + keyPrefix + ":" + nowDate);

        // ③ 拼接：时间戳左移 32 位，再与序列号做或运算
        return (timestamp << BIT_SIZE) | increment;
    }
}
```

### 位运算图解

```
假设：timestamp = 1000，increment = 5

第 ① 步：timestamp << 32   （把时间戳挪到高位）
┌────────────────────────────┬────────────────────────────┐
│      时间戳 (高位)          │      0000...0000 (低位)     │
└────────────────────────────┴────────────────────────────┘

第 ② 步：| increment        （把序列号填到低位）
┌────────────────────────────┬────────────────────────────┐
│      时间戳 (高位)          │      序列号 (低位)          │
└────────────────────────────┴────────────────────────────┘
     共 63 位（long 是 64 位，最高位是符号位，不用）

最终 ID = timestamp * 2^32 + increment
```

**🧠 记忆：`<< 32` 是把时间戳"抬高"，`| increment` 是把序列号"塞进低 32 位"。**

### 🎯 为什么这样设计

| 设计 | 原因 |
|---|---|
| **时间戳在高位** | 保证 ID **整体趋势递增**（对 MySQL B+ 树索引友好） |
| **序列号在低位** | 同一秒内产生的 ID 靠序列号区分，也递增 |
| **减 EPOCH** | 缩小时间戳数值，31 位够用 69 年，省位数 |
| **序列号 key 带日期** | 每天从 0 重新计数，避免 32 位溢出（2^32 ≈ 42.9 亿/天足够） |
| **用 Redis INCR** | 原子自增，多实例部署也不会重复 |

### ⚠️ 三个必须注意的点

**① ID 是 `long`，但可能超出 JavaScript 精度范围**

```
生成的 ID 形如：97087344884056080
超过 2^53 - 1 = 9007199254740991（JS Number 的精确上限）
```

**后果：** 前端拿到的 ID **末几位会变成 0**，订单号对不上。

**解决（推荐全局配置）：**
```java
@Configuration
public class JacksonConfig {
    @Bean
    public Jackson2ObjectMapperBuilderCustomizer customizer() {
        return builder -> builder
                .serializerByType(Long.class, ToStringSerializer.instance)
                .serializerByType(Long.TYPE, ToStringSerializer.instance);
    }
}
```
> 也可以给单个字段加 `@JsonSerialize(using = ToStringSerializer.class)`，或在 Controller 里 `String.valueOf(orderId)`。

**② `increment` 可能为 null**

```java
Long increment = stringRedisTemplate.opsForValue().increment(...);
return (timestamp << BIT_SIZE) | increment;    // ⚠️ increment 为 null 会 NPE
```

**稳妥写法：**
```java
if (increment == null) {
    throw new RuntimeException("生成订单号失败：Redis 自增返回 null");
}
```

**③ `|` 和 `+` 的区别**

当低 32 位没有溢出时，`|` 和 `+` 结果相同。
但 **`|` 更安全**：即使 increment 意外超过 2^32，`|` 是"按位或"不会进位污染时间戳部分；而 `+` 会**进位**导致 ID 错乱。

### 🎯 面试延伸：为什么不用数据库自增？

| | 数据库自增 | Redis 自增 |
|---|---|---|
| **业务量泄露** | ❌ 对手能从订单号算出你一天多少单 | ✅ 时间戳在高位，看不出单量 |
| **分库分表** | ❌ 多库会重复 | ✅ 天然全局唯一 |
| **性能** | 低（要走数据库） | ✅ 高（Redis 内存操作） |
| **有序性** | ✅ | ✅ 趋势递增 |

---

## A5 · 逻辑过期重建缓存的并发细节 🎯

### 代码回顾

```java
public Shop queryWithLogicalExpire(Long id) {
    String shopJson = stringRedisTemplate.opsForValue().get(CACHE_SHOP_KEY + id);
    if (StrUtil.isBlank(shopJson)) {
        return null;
    }
    RedisData redisData = JSONUtil.toBean(shopJson, RedisData.class);
    JSONObject data = (JSONObject) redisData.getData();
    Shop shop = JSONUtil.toBean(data, Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();

    // ① 未过期 → 直接返回
    if (expireTime.isAfter(LocalDateTime.now())) {
        return shop;
    }

    // ② 已过期 → 抢锁重建
    boolean lock = tryLock(LOCK_SHOP_KEY + id);
    if (lock) {
        CACHE_REBUILD_EXECUTOR.submit(() -> {
            try {
                this.saveShop2Redis(id, 20L);   // 查库 + 写回缓存
            } catch (Exception e) {
                throw new RuntimeException(e);
            } finally {
                unlock(LOCK_SHOP_KEY + id);     // ★ 必须在 finally 解锁
            }
        });
    }

    // ③ ★ 不管有没有抢到锁，都返回旧数据
    stringRedisTemplate.opsForValue().set(CACHE_SHOP_KEY + id,
            JSONUtil.toJsonStr(shop), CACHE_SHOP_TTL, TimeUnit.MINUTES);
    return shop;
}
```

### ⚠️ 第 ③ 步的问题（本项目代码里的隐藏 bug）

看最后那句 `set`：它把**剥掉 `RedisData` 包装的裸 `shop`** 又写回了缓存！

**后果：** 下一次请求进来时，`JSONUtil.toBean(shopJson, RedisData.class)` 会解析不到 `data` 和 `expireTime` 字段，**逻辑过期机制彻底失效**（退化成普通缓存）。

**✅ 正确的做法有两种：**

**方案 A：删掉这行（推荐）**
```java
    // ③ 直接返回旧数据，让后台线程去重建，这里不要写缓存
    return shop;
}
```

**方案 B：如果要写，必须写回完整的 `RedisData` 结构**
```java
    RedisData newData = new RedisData();
    newData.setData(shop);
    newData.setExpireTime(expireTime);      // 保持原来的逻辑过期时间
    stringRedisTemplate.opsForValue().set(CACHE_SHOP_KEY + id,
            JSONUtil.toJsonStr(newData));
    return shop;
```

### 🎯 这段代码的设计精髓

```
读请求进来
  ├─ 数据未过期 ──────────────→ 直接返回 ✅（无锁、无等待）
  └─ 数据已过期
       ├─ 抢到锁 ─→ 丢给线程池异步重建（当前请求不等待）
       └─ 没抢到 ─→ 说明别人在重建
                     ↓
              不管哪种情况，都立即返回【旧数据】
```

**核心思想：宁可给用户看一会儿旧数据，也绝不让请求排队等锁。**

**🎯 对比互斥锁方案：**

| | 互斥锁 | 逻辑过期 |
|---|---|---|
| 过期后第一个请求 | 查库（慢） | 返回旧数据（快） |
| 过期后其他请求 | **阻塞等待**（更慢） | 返回旧数据（快） |
| 数据一致性 | 强一致 | 最终一致 |
| 适用 | 一致性要求高 | **热点 key，追求吞吐** |

### ⚠️ 三个必须注意的细节

| 细节 | 说明 |
|---|---|
| **① 缓存预热** | 逻辑过期方案**必须提前把数据放进缓存**（否则 `isBlank` 直接返回 null，永远查不到） |
| **② 线程池大小** | `Executors.newFixedThreadPool(10)` 是硬编码，**生产环境应该可配置**，且要用有界队列防止 OOM |
| **③ 解锁必须 finally** | 重建抛异常时如果不解锁，锁会占满 10 秒（TTL），期间所有请求都拿到旧数据 |

### ⚠️ 互斥锁方案里的另一个隐患

```java
if (!b) {
    Thread.sleep(50);
    return queryWithMutex(id);      // ★ 递归重试
}
```

**问题：** 并发极高时，递归会**不断堆栈**，有 **StackOverflowError** 风险。

**✅ 更稳的写法：用循环重试 + 最大次数限制**
```java
for (int i = 0; i < 3; i++) {
    if (tryLock(LOCK_SHOP_KEY + id)) {
        // 抢到锁，查库
        break;
    }
    Thread.sleep(50);
}
```

---

## A6 · 拦截器 + ThreadLocal 的完整生命周期 🎯

### 两个拦截器为什么这么设计

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // ① order(0)：所有请求都走，负责【查用户 + 续期】
        registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate))
                .addPathPatterns("/**")
                .order(0);

        // ② order(1)：负责【校验是否登录】，放行白名单
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns(
                        "/shop/**",
                        "/voucher/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/blog/hot",
                        "/user/code",
                        "/user/login",
                        "/error"                    // ★ 必须放行，否则错误信息会被吞
                )
                .order(1);
    }
}
```

### 完整执行流程

```
请求进来
  │
  ├─→ RefreshTokenInterceptor.preHandle()   [order=0]
  │     ├─ 取请求头 authorization
  │     ├─ 是空 → 直接 return true（放行，交给下一个拦截器）
  │     ├─ 查 Redis: login:token:{token}
  │     │    ├─ 空 → return true（放行，LoginInterceptor 会拦）
  │     │    └─ 有 → BeanUtil.fillBeanWithMap → UserHolder.saveUser(userDto)
  │     │              → expire 续期 3600s
  │     └─ return true
  │
  ├─→ LoginInterceptor.preHandle()          [order=1]
  │     ├─ UserHolder.getUser() == null ?
  │     │    ├─ 是 → response.setStatus(401); return false  【请求结束】
  │     │    └─ 否 → return true
  │
  ├─→ Controller / Service  【业务代码可以从 UserHolder 取用户】
  │
  └─→ RefreshTokenInterceptor.afterCompletion()   [★ 无论如何都会执行]
        └─ UserHolder.removeUser()   清理 ThreadLocal
```

### 🎯 为什么必须清理 ThreadLocal

| 不清理的后果 | 说明 |
|---|---|
| **内存泄漏** | Tomcat 线程池的线程是**长期存活**的，ThreadLocal 里的对象不会被回收 |
| **数据串号** | 最危险！线程 A 用完没清理，线程被复用处理线程 B 的请求时，**B 读到了 A 的用户信息** |
| **权限绕过** | 用户 B 可能以用户 A 的身份操作 |

**⚠️ 重要认知：Tomcat 每次请求结束时**不会**自动清空 ThreadLocal。必须靠 `afterCompletion` 手动清理。

### ⚠️ 本项目踩坑回顾：两个 ThreadLocal 搞混

```java
// ❌ 曾经的错误设计
private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();
private static final ThreadLocal<User>   t2 = new ThreadLocal<>();

public static void saveUser(UserDTO user){ tl.set(user); }   // 写 tl
public static UserDTO getUser(){ return tl.get(); }          // 读 tl
public static void save(User user){ t2.set(user); }          // 写 t2
public static User get(){ return t2.get(); }                 // 读 t2
```

**症状：** 登录校验通过（都读 `t2`），但业务代码 `getUser()` 读 `tl` → **永远 null** → NPE。

**✅ 正确设计：一个 ThreadLocal 只存一种类型**
```java
public class UserHolder {
    private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();
    public static void saveUser(UserDTO user) { tl.set(user); }
    public static UserDTO getUser() { return tl.get(); }
    public static void removeUser() { tl.remove(); }
}
```

**🧠 记忆：ThreadLocal 要么只有一个，要么名字必须一眼能分辨归属。**

### ⚠️ 拦截器里 preHandle 返回 false 时 afterCompletion 会执行吗

**会，但有条件：**

```
拦截器 A（order=0）preHandle → true
拦截器 B（order=1）preHandle → false
  ↓
只有 A 的 afterCompletion 会执行     ← B 的不会（因为 B 没"进入"）
```

**规则：`afterCompletion` 只会对"成功执行过 preHandle 且返回 true"的拦截器调用。**

### ⚠️ 后台线程取不到 UserHolder

```java
// ❌ 后台线程里这样写会 NPE
public void handleVoucherOrder(VoucherOrder voucherOrder) {
    Long userId = UserHolder.getUser().getId();   // ★ null！
}
```

**原因：** `UserHolder` 是 ThreadLocal，**只在处理请求的那个线程里有值**。
后台线程（`pool-5-thread-1`）从没经过拦截器，ThreadLocal 是空的。

**✅ 解决：把 userId 打包进订单对象，跨线程传递**

```java
// 请求线程里：把 userId 塞进订单对象
VoucherOrder voucherOrder = new VoucherOrder();
voucherOrder.setUserId(UserHolder.getUser().getId());
voucherOrder.setVoucherId(voucherId);
orderTasks.add(voucherOrder);

// 后台线程里：从订单对象取，而不是从 UserHolder 取
public void handleVoucherOrder(VoucherOrder voucherOrder) {
    Long userId = voucherOrder.getUserId();   // ★ 正确
}
```

**🧠 记忆：ThreadLocal 不能跨线程！异步场景必须把上下文打包进消息体。**

---

## A7 · CacheClient 泛型 + 函数式接口设计 🎯

### 完整代码

```java
@Component
public class CacheClient {

    private final StringRedisTemplate stringRedisTemplate;

    public CacheClient(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    /**
     * 【缓存穿透方案】
     * @param keyPrefix  缓存 key 前缀
     * @param id         业务 id
     * @param type       返回类型（泛型）
     * @param dbFallback 查库逻辑（★ 函数式接口，由调用方传入）
     * @param time       TTL
     * @param unit       时间单位
     */
    public <R, ID> R queryWithPassThrough(
            String keyPrefix, ID id, Class<R> type,
            Function<ID, R> dbFallback,           // ★ 关键：函数式接口
            Long time, TimeUnit unit) {

        String key = keyPrefix + id;

        // ① 查缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        if (StrUtil.isNotBlank(json)) {
            return JSONUtil.toBean(json, type);      // 命中真实数据
        }

        // ② 空字符串 → 之前缓存过"不存在"，直接返回 null（防穿透）
        if (json != null) {
            return null;
        }

        // ③ 缓存未命中 → 回调查库
        R r = dbFallback.apply(id);

        // ④ 数据库也没有 → 缓存空值（防穿透）
        if (r == null) {
            stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
            return null;
        }

        // ⑤ 数据库有 → 写入缓存
        this.set(key, r, time, unit);
        return r;
    }
}
```

### 🎯 为什么这样设计（设计模式解析）

**问题：** 如果每个业务都写一遍缓存逻辑，会有大量重复代码：
```
ShopService 里的缓存逻辑
UserService 里的缓存逻辑
BlogService 里的缓存逻辑
... 全是 90% 相同的代码
```

**解决：把"变化的"抽出来当参数（策略模式 + 模板方法）**

| 部分 | 是否变化 | 怎么处理 |
|---|---|---|
| 查缓存 → 未命中查库 → 写缓存 | **不变** | 封装在 `CacheClient` 里 |
| 查库的具体逻辑 | **变化** | 用 `Function<ID, R>` 由调用方传入 |
| 缓存 key 前缀 | **变化** | 参数传入 |
| 返回类型 | **变化** | 泛型 `<R>` |

### 调用方怎么用

```java
// ShopServiceImpl
Shop shop = cacheClient.queryWithPassThrough(
        CACHE_SHOP_KEY,              // key 前缀
        id,                          // id
        Shop.class,                  // 返回类型
        this::getById,               // ★ 方法引用 = 查库逻辑
        CACHE_SHOP_TTL, TimeUnit.MINUTES);
```

**`this::getById` 是什么？**
- `this` 是 `ShopServiceImpl` 实例
- `getById` 是 MyBatis-Plus 提供的 `ServiceImpl.getById(Serializable id)`
- 方法引用**自动匹配** `Function<ID, R>` 接口（输入 id，返回 Shop）

**🎯 等价写法对比：**

```java
// ① 方法引用（最简洁，推荐）
this::getById

// ② Lambda
id -> this.getById(id)

// ③ 匿名内部类（最啰嗦）
new Function<Long, Shop>() {
    @Override
    public Shop apply(Long id) {
        return getById(id);
    }
}
```

### ⚠️ 泛型的两个关键点

**① 为什么要传 `Class<R> type`？**

因为**泛型擦除**！`JSONUtil.toBean(json, Shop.class)` 需要一个真实的 Class 对象来反序列化。
运行时泛型 `R` 已经变成 `Object` 了，**拿不到具体类型**。

**这就是经典的"泛型擦除问题"**：编译期有泛型，运行期没了。所以需要额外传 Class 对象。

**② 方法引用能简化的前提是 `@FunctionalInterface`**

`java.util.function.Function<T, R>` 只有一个抽象方法 `apply`，所以可以用方法引用/Lambda 简化。

**🧠 记忆：泛型 + 函数式接口 = 把"变化的逻辑"变成参数传进来，消除重复代码。**

---

# 附录 B · 坑位大全（按严重程度分级）

> 把踩过的坑**按危险程度排序**，并给出"如何提前发现"。
>
> **🔇 标记 = 静默错误**：不报错、能编译、能启动，但逻辑是错的——**最难查的一类**。

## P0 · 致命坑（导致数据错误或服务不可用）

### P0-1 🔇 秒杀接口没判空，`voucher` 为 null 直接 NPE

```java
// ❌ 危险写法
SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {   // ★ NPE！
```

**症状：** 传一个**不存在的券 id** → `NullPointerException` → 被全局异常处理器捕获 → 返回 `{"success":false,"errorMsg":"服务器异常"}`。

**危害：** 用户传错参数就能让接口报错，且错误信息**完全没有指向性**。

**✅ 正确写法：**
```java
SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
if (voucher == null) {
    return Result.fail("优惠券不存在");
}
if (voucher.getBeginTime().isAfter(LocalDateTime.now())) { ... }
```

**🎯 如何提前发现：** 所有 `getById()` 之后**第一件事就是判空**，这是铁律。

---

### P0-2 🔇 Lua 脚本路径和文件位置不一致

```java
// 代码写的是 classpath 根目录
UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
```
```
// 文件实际位置
src/main/resources/mapper/unlock.lua
     ↓ 编译后
target/classes/mapper/unlock.lua     ← 文件在这里
target/classes/unlock.lua            ← 代码找的是这里，不存在！
```

**症状：**
```
FileNotFoundException: class path resource [unlock.lua] cannot be opened
  because it does not exist
```

**✅ 正确做法（三选一）：**
```java
// ① 路径和位置一致
new ClassPathResource("mapper/unlock.lua")

// ② 把文件挪到 resources 根目录（推荐，Lua 不属于 mapper）
new ClassPathResource("unlock.lua")

// ③ 用 @Value 从配置注入（最灵活）
@Value("classpath:lua/unlock.lua")
private Resource unlockScript;
```

**🎯 如何提前发现：** 编译后 `dir target\classes` 看一眼 Lua 文件的**实际层级**。

---

### P0-3 🔇 消费组没创建，消费者空转死循环

```lua
-- ❌ Lua 里只 XADD 不建组
redis.call('xadd', 'stream.orders', '*', ...)
```
```java
// ❌ Java 侧直接读，没建组
stringRedisTemplate.opsForStream().read(
        Consumer.from("g1", "c1"),       // ← g1 不存在！
        ...);
```

**症状：**
```
NOGROUP No such key 'stream.orders' or consumer group 'g1' in XREADGROUP with GROUP option
```
日志**每 30ms 刷一行**，5 秒 3600 行，后台线程**永远卡死**，订单全不落库。

**✅ 正确做法：**
```java
@PostConstruct
public void init() {
    try {
        stringRedisTemplate.opsForStream()
                .createGroup(queueName, ReadOffset.from("0"), "g1");
    } catch (Exception e) {
        log.info("消费组已存在：{}", e.toString());
    }
    executor.submit(new Handler());     // ★ 建组之后再启动
}
```

**🎯 如何提前发现：** 启动后立刻执行 `redis-cli xinfo groups stream.orders`，**看不到组就是有问题**。

---

### P0-4 🔇 `while(true)` + catch + sleep，没有 break 出口

```java
// ❌ 日志炸弹
public void handlePendingList() {
    while (true) {
        try {
            read(...);
        } catch (Exception e) {
            log.error("处理挂起订单列表发生异常");   // 没传 e
            Thread.sleep(20);                        // 睡 20ms 继续
        }
    }
}
```

**症状：** 一旦进入异常分支，**永远出不来**，CPU 空转 + 日志撑爆磁盘。

**✅ 正确写法：**
```java
while (true) {
    try {
        List<...> list = read(...);
        if (list == null || list.isEmpty()) {
            break;                       // ★ 唯一的出口
        }
        handle(list.get(0));
    } catch (Exception e) {
        log.error("处理挂起订单列表发生异常", e);   // ★ 带上 e
        try {
            Thread.sleep(20);
        } catch (InterruptedException ex) {
            Thread.currentThread().interrupt();      // ★ 恢复中断标志
            break;                                    // ★ 中断就退出
        }
    }
}
```

**🎯 如何提前发现：** 写 `while(true)` 时**先想好出口在哪**；`Thread.sleep` 的 catch 里**必须处理中断**。

---

### P0-5 🔇 事务失效：`@Transactional` 方法被类内部调用

```java
@Service
public class VoucherOrderServiceImpl {

    public Result seckillVoucher(Long voucherId) {
        // ❌ 类内部调用，不走代理，@Transactional 失效
        return this.createVoucherOrder(voucherId);
    }

    @Transactional
    public Result createVoucherOrder(Long voucherId) {
        // 这里的数据库操作【不在事务里】！
    }
}
```

**症状：** **不报错**！但事务**没生效**——扣库存成功、创建订单失败时**不会回滚**。

**危害：** **极高**（静默错误，数据可能不一致）。

**✅ 三种解法：**

```java
// 方案 1：AopContext（要求 exposeProxy = true）
@EnableAspectJAutoProxy(exposeProxy = true)   // 启动类上
IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
proxy.createVoucherOrder(voucherId);

// 方案 2：自身注入（★ 推荐，后台线程也能用）
@Resource
@Lazy
private IVoucherOrderService selfProxy;
selfProxy.createVoucherOrder(voucherId);

// 方案 3：把方法挪到另一个 Service 里
otherService.createVoucherOrder(voucherId);
```

**⚠️ 特别注意：`AopContext.currentProxy()` 在后台线程会失败！**
```
IllegalStateException: Cannot find current proxy: Set 'exposeProxy' property on Advised to 'true' ...
```
因为它依赖 **ThreadLocal**，只有"当前线程经过代理入口"才有值。

**🎯 如何提前发现：**
1. 加了 `@Transactional` 的方法，检查调用方是**同类内部**还是**通过代理**
2. 压测时故意让事务中间失败，看**会不会回滚**

---

### P0-6 🔇 `excludePathPatterns` 路径少了开头的 `/`

```java
.excludePathPatterns(
        "/shop/**",
        "upload/**",      // ❌ 少了 /
        "blog/hot",       // ❌ 少了 /
        "/user/code",
        "/user/login"
)
```

**症状：** `/blog/hot` 明明在放行名单里，**却返回 401**。

**原因：** Spring Boot 3 的 `PathPatternParser` 要求路径以 `/` 开头。

**✅ 正确：** 全部改成 `"/upload/**"`、`"/blog/hot"`。

**🎯 如何提前发现：** 逐个测试放行名单里的接口**不带 token 能不能访问**。

---

### P0-7 🔇 后端异常被伪装成 401，错误信息完全丢失

```
业务抛异常 → Spring 返回 500
          → 转发到 /error 重新派发
          → /error 不在放行名单
          → LoginInterceptor 拦截
          → setStatus(401) + 清空 body
          ↓
      前端看到：401，响应体为空
```

**症状：** 明明后端报错，前端只看到 401，**完全不知道真实原因**。

**✅ 正确做法：把 `/error` 加入放行名单**
```java
.excludePathPatterns(..., "/error")
```

**🎯 如何提前发现：**
- 排查 401 时，先确认是"真的未登录"还是"错误被改写"
- 加一个全局异常处理器**同时打日志到文件**，不完全依赖 HTTP 响应

---

## P1 · 重要坑（导致业务异常或一致性/安全问题）

### P1-1 🔇 Redis 和 MySQL 数据不一致（异步秒杀的固有代价）

**症状：**
```
Redis 库存 = 95     MySQL 库存 = 100     差 5
```

**原因链：**
- Lua 脚本扣了 Redis 库存
- 但订单**没进队列** / 后台线程**没消费** / **落库失败** → MySQL 没扣

**危害：** 用户看到"抢购成功"但实际没下单。

**✅ 排查命令：**
```bash
# ① 检查队列是否有积压
redis-cli xlen stream.orders
redis-cli xpending stream.orders g1

# ② 检查已购集合和订单数是否一致
redis-cli scard seckill:order:12
mysql -e "select count(*) from tb_voucher_order where voucher_id=12"
```

**✅ 对账 SQL：**
```sql
-- Redis 已购用户数 应该 == MySQL 订单数
SELECT COUNT(*) FROM tb_voucher_order WHERE voucher_id = 12;
-- MySQL 库存 应该 == Redis 库存
SELECT stock FROM tb_seckill_voucher WHERE voucher_id = 12;
```

**🎯 生产建议：**
1. 用**定时任务对账**（每隔几分钟比对 Redis 和 MySQL）
2. 记录**操作日志**，便于补偿
3. 关键业务用**可靠消息队列**（RocketMQ / Kafka）而不是 Redis Stream

---

### P1-2 🔇 雪花 ID 返回给前端精度丢失

```javascript
// 后端返回
{"success": true, "data": 97087344884056080}
                 ↓ 前端 JSON.parse
{"success": true, "data": 97087344884056000}   // ★ 末位变 0 了！
```

**原因：** JS 的 `Number` 是双精度浮点，**只能精确表示 2^53 - 1 以内的整数**。

**✅ 解决：把 Long 序列化成 String**（见 A4）

**🎯 如何提前发现：** ID 超过 `9007199254740991`（16 位数字）就要警惕。

---

### P1-3 🔇 验证码为 null 时 NPE（逻辑侥幸正确）

```java
String redisCode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY + phone);
if (!code.equals(redisCode) || redisCode == null) {   // ★ 顺序反了
    return Result.fail("验证码错误");
}
```

**分析：**
- 当 `redisCode == null`（验证码过期）时，`code.equals(null)` 返回 **false** → `!false = true` → **短路**，后面不再执行 → 返回"验证码错误" → **结果是对的**
- **但这是"侥幸正确"**：如果哪天改成 `redisCode.equals(code)` 就变 NPE

**✅ 更稳的写法（null 判断放前面）：**
```java
if (redisCode == null || !redisCode.equals(code)) {
    return Result.fail("验证码错误");
}
```

**🎯 如何提前发现：** `equals` 调用方**优先用不会为 null 的那个**，或者用 `Objects.equals(a, b)`。

---

### P1-4 🔇 缓存集合类 key 没有 TTL，无限增长

```java
// 没设过期时间
stringRedisTemplate.opsForSet().add(BLOG_LIKED_KEY + id, userId);
stringRedisTemplate.opsForZSet().add(FEED_KEY + userId, blogId, timestamp);
```

**症状：** 用户和博客越来越多 → Redis 内存被这些集合撑满 → **触发内存淘汰，把热点缓存 key 挤掉**（缓存命中率暴跌）。

**✅ 建议：**
1. **能给 TTL 就给 TTL**（如 Feed 流保留最近 30 天）
2. 定期清理（定时任务）
3. 监控 `INFO memory` 的 `used_memory`

---

### P1-5 🔇 Redis 里存了 `User` 实体，泄露密码

```java
// ❌ 危险：User 里有 password 字段
Map<String, Object> userMap = BeanUtil.beanToMap(user, ...);
stringRedisTemplate.opsForHash().putAll(LOGIN_USER_KEY + token, userMap);
```

**症状：**
1. `redis-cli hgetall login:token:xxx` 能直接看到密码字段
2. `/user/me` 接口可能把 password 返回给前端

**✅ 正确：存 `UserDTO`（只保留 id / nickName / icon）**
```java
UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, ...);
```

**🎯 如何提前发现：** `redis-cli hgetall login:token:xxx` 看一眼实际存了什么。

---

### P1-6 🔇 `log.error` 没传异常对象

```java
// ❌ 只看到"出错了"，看不到是什么错
log.error("处理挂起订单列表发生异常");

// ✅ 完整堆栈
log.error("处理挂起订单列表发生异常", e);
```

**症状：** 本项目因为这个问题，`NOGROUP` 错误被吞了**很久才定位到**。

**🎯 铁律：`log.error` 的最后一个参数永远传异常对象。**

---

### P1-7 🔇 `e.getMessage()` 信息不全

```java
// ❌ 输出：Error in execution
log.info("消费组已存在：{}", e.getMessage());

// ✅ 输出：RedisSystemException: Error in execution; nested exception is
//        io.lettuce.core.RedisCommandExecutionException: BUSYGROUP Consumer Group name already exists
log.info("消费组已存在：{}", e.toString());
```

**原因：** Spring 的 `DataAccessException` 是**包装异常**，`getMessage()` 只返回最外层信息，
**根因在 `getCause()` 里**。

**✅ 三种写法：**
```java
// ① toString()：包含嵌套异常链
log.info("...：{}", e.toString());

// ② 直接传异常对象（推荐）
log.warn("消费组已存在", e);

// ③ 手动取根因
Throwable root = e;
while (root.getCause() != null) {
    root = root.getCause();
}
log.info("根因：{}", root.getMessage());
```

---

### P1-8 🔇 「两条一样的 if」其实是必要的

```java
String shopJson = stringRedisTemplate.opsForValue().get(key);

if (StrUtil.isNotBlank(shopJson)) {
    return JSONUtil.toBean(shopJson, Shop.class);   // ① 命中真实数据
}

if (shopJson != null) {                              // ② ★ 不是冗余！
    return null;                                      //    命中"空值缓存"
}

// 走到这里 = shopJson 为 null = 缓存没命中 → 查库
Shop shop = getById(id);
```

**为什么两个 if 都要：**

| `shopJson` 的值 | 第一个 if | 第二个 if | 结果 |
|---|---|---|---|
| `'{"id":1,...}'` | ✅ 进入 → 返回对象 | — | 正常 |
| `""`（缓存空对象） | ❌ 跳过 | ✅ 进入 → 返回 null | **防穿透生效** |
| `null`（未缓存） | ❌ 跳过 | ❌ 跳过 | 查库 |

**🧠 记忆：`isNotBlank` 用来区分"有数据"和"空字符串"；`!= null` 用来区分"空字符串"和"没缓存"。**

---

## P2 · 性能与运维坑

### P2-1 `KEYS *` 阻塞整个 Redis

```bash
# ❌ 生产禁用！遍历所有 key，期间阻塞所有请求
KEYS user:*

# ✅ 用 SCAN 分批游标遍历
SCAN 0 MATCH user:* COUNT 100
```

**🎯 面试点：为什么 `SCAN` 不阻塞？**
> `SCAN` 每次只返回一小批（默认 10 个），并且**不保证返回所有 key**（可能漏、可能重复），
> 但换来的是**不会长时间阻塞**。

---

### P2-2 大 key 用 `DEL` 删除会阻塞

```bash
# ❌ 删除百万成员的 Set，阻塞几秒
DEL big:set

# ✅ 用 UNLINK 异步删除（Redis 4.0+）
UNLINK big:set
```

**原理：** `UNLINK` 只把 key 从键空间**摘除**，真正的内存回收交给**后台线程**。

---

### P2-3 `SAVE` 会阻塞主线程

```bash
# ❌ 阻塞主线程直到 RDB 写完
SAVE

# ✅ fork 子进程异步写（推荐）
BGSAVE
```

**⚠️ 配置里的 `save 900 1` 是自动触发 BGSAVE，和 `SAVE` 命令不是一回事。**

---

### P2-4 Pipeline 一次发太多命令撑爆缓冲区

```java
// ❌ 一次发 100 万条，客户端缓冲区可能 OOM
stringRedisTemplate.executePipelined(connection -> {
    for (int i = 0; i < 1_000_000; i++) { ... }
    return null;
});

// ✅ 分批，每批 500 条
List<List<Integer>> batches = Lists.partition(ids, 500);
for (List<Integer> batch : batches) {
    stringRedisTemplate.executePipelined(connection -> {
        for (Integer id : batch) { ... }
        return null;
    });
}
```

---

### P2-5 持久化配置不当导致数据丢失

| 场景 | 危险配置 | 后果 |
|---|---|---|
| 把 Redis 当**数据库**用 | 关 RDB + 关 AOF | **重启数据全丢** |
| 追求性能 | `appendfsync no` | 宕机可能丢**大量**数据 |
| 只开 RDB | `save 900 1` | 最多丢 15 分钟数据 |

**✅ 生产建议：**
```conf
appendonly yes
appendfsync everysec          # 最多丢 1 秒
save 900 1
save 300 10
save 60 10000
```

---

### P2-6 危险命令没禁用 + 没设密码 + 暴露公网

```conf
# ❌ 默认配置暴露公网 = 挖矿木马的最爱
bind 0.0.0.0
protected-mode no
# 没设 requirepass
```

**✅ 生产加固清单：**
```conf
# ① 绑定内网地址
bind 192.168.1.100

# ② 设置强密码
requirepass 复杂随机密码

# ③ 重命名/禁用危险命令
rename-command FLUSHALL ""
rename-command FLUSHDB ""
rename-command KEYS ""
rename-command CONFIG "CONFIG_a1b2c3"

# ④ 不用默认端口
port 16379

# ⑤ 用非 root 用户运行
```

**🎯 面试点：Redis 未授权访问漏洞**
> 默认配置下 Redis 无密码 + 绑定 `0.0.0.0`，攻击者可直接连接并：
> 1. 通过 `CONFIG SET dir` + `CONFIG SET dbfilename` 写入 SSH 公钥到 `~/.ssh/authorized_keys`
> 2. 写入 crontab 反弹 shell
> 3. 写入 WebShell 到网站目录

---

## P3 · 思维误区（不是代码问题，是认知问题）

### P3-1 「配置看起来对」≠「配置生效了」

**案例：** JMeter 里写了 `${token}`，看起来有值，实际变量未定义 → **原样发送 `${token}`** → 401。

**教训：** 未定义的变量**不会报错**，而是**原样输出**。这适用于 JMeter、Apifox、Nginx、Spring 的 `@Value` 等**所有占位符机制**。

**🎯 验证方法：** 看**实际发出的请求**（抓包 / 服务端日志），而不是看**配置文件**。

---

### P3-2 「代码改了」≠「生效了」

**案例：** 改完源码后没重新编译/重启，测试的还是旧代码。

**🎯 验证方法：** 对比**源码修改时间** vs **class 编译时间** vs **进程启动时间**：

```powershell
(Get-Item 'src/.../Xxx.java').LastWriteTime              # 源码
(Get-Item 'target/classes/.../Xxx.class').LastWriteTime  # 编译产物
(Get-Process -Id $pid).StartTime                          # 进程
```

**三者顺序必须是：源码 ≤ class ≤ 进程启动时间。**

---

### P3-3 「异常被 catch 了」≠「问题解决了」

**案例：** `catch (Exception e) { log.error("出错了"); }` —— 异常被吞，问题被掩盖。

**🎯 原则：**
1. catch 里**必须记录完整异常**（传 `e`）
2. **不要用 catch 兜住所有异常后继续**（尤其在有状态机的循环里）
3. 该抛就抛（`throw new RuntimeException(e)`），让上层知道

---

### P3-4 「代码能跑」≠「逻辑正确」

**案例：**
- 验证码校验 `!code.equals(redisCode) || redisCode == null` 顺序反了但**结果侥幸正确**
- 乐观锁 `gt("stock", 0L)` 之前是裸的 `stock - 1`，**能跑但会超卖**

**🎯 验证方法：**
- **看边界**：null / 空 / 负数 / 超大值
- **看并发**：用 JMeter 压测，对账数据库
- **看异常路径**：让中间步骤失败，看是否回滚

---

### P3-5 「日志刷屏」≠「压力大」

**案例：** 3000+ 行/分钟的日志，看起来像高负载，**其实是死循环刷错误**。

**🎯 排查方法：**
```powershell
# 看是不是同一个错误在重复
Get-Content log.txt | Select-String 'ERROR' | Group-Object | Sort-Object Count -Descending
```

**如果同一条错误出现几千次 → 是死循环，不是高负载。**

---

# 附录 C · 交付前自查清单 + 调试工具速查

> 每次写完功能、上线前，照着过一遍。

## C1 · Java 代码自查

| # | 检查项 | 对应的坑 |
|---|---|---|
| 1 | 所有 `getById()` 之后**判空**了吗？ | P0-1 |
| 2 | 加了 `@Transactional` 的方法，调用方**走代理**了吗？ | P0-5 |
| 3 | ThreadLocal 在 `afterCompletion` 里**清理**了吗？ | A6 |
| 4 | 后台线程取用户信息，是从**参数**取还是从 `UserHolder` 取？ | A6 |
| 5 | `while(true)` 有**出口**吗？ | P0-4 |
| 6 | `Thread.sleep` 的 catch 里**处理中断**了吗？ | P0-4 |
| 7 | 所有 `log.error` 都**传了异常对象**吗？ | P1-6 |
| 8 | 用 `e.getMessage()` 的地方，**信息够吗**？ | P1-7 |
| 9 | Lua 脚本路径和**编译后的实际位置**一致吗？ | P0-2 |
| 10 | 消息队列的**消费组创建**了吗？在启动前吗？ | P0-3 |

## C2 · Redis 使用自查

| # | 检查项 | 为什么 |
|---|---|---|
| 1 | key 命名遵循 `业务:类型:id` 吗？ | 可读性 |
| 2 | key 都**设了 TTL** 吗？（除必要的集合） | 防内存泄漏 |
| 3 | 有没有 **BigKey**？（`redis-cli --bigkeys`） | 防阻塞 |
| 4 | 需要原子性的多步操作，用 **Lua 脚本**了吗？ | 防并发问题 |
| 5 | 分布式锁的解锁在 **finally** 里吗？ | 防锁泄漏 |
| 6 | 锁的 value 有**线程标识**吗？ | 防误删别人的锁 |
| 7 | 存的是 **DTO** 还是 **Entity**？（防泄露密码） | 安全 |
| 8 | 缓存更新用的是**删除**而不是**更新**吗？ | 一致性 |
| 9 | 缓存空对象的 TTL 比正常数据**短**吗？ | 防脏数据停留过久 |
| 10 | 大批量操作用 **Pipeline** 了吗？分批了吗？ | 性能 |

## C3 · Spring 集成自查

| # | 检查项 | 对应的坑 |
|---|---|---|
| 1 | `excludePathPatterns` 的路径都以 `/` 开头吗？ | P0-6 |
| 2 | `/error` 加入放行名单了吗？ | P0-7 |
| 3 | 拦截器加了 `order()` 吗？顺序对吗？ | A6 |
| 4 | 用的是 `StringRedisTemplate` 还是 `RedisTemplate`？ | 序列化 |
| 5 | 序列化器配置对了吗？（key 用 String） | 可读性 |
| 6 | Long 类型的 ID **序列化成 String** 了吗？ | P1-2 |
| 7 | 分布式锁用的是 `redisson` 还是 `redisson-spring-boot-starter`？ | **版本兼容** |

## C4 · 秒杀专项自查

| # | 检查项 | 为什么 |
|---|---|---|
| 1 | 库存判断和扣减**在一条 SQL** 里吗？ | 防超卖 |
| 2 | `WHERE` 里有 `stock > 0` 吗？ | 防超卖 |
| 3 | 一人一单的判断和记录**原子**吗？ | 防重复下单 |
| 4 | 订单**进队列**了吗？（别只创建对象忘了 add） | 防丢失 |
| 5 | 队列的**消费者启动**了吗？ | 防积压 |
| 6 | 消费者 ACK 放在**业务成功之后**吗？ | 防消息丢失 |
| 7 | 压测后 **Redis 库存 == MySQL 库存** 吗？ | 一致性 |
| 8 | 压测后 **MySQL 库存 ≥ 0** 吗？ | 防超卖 |
| 9 | 压测后**订单数 == 成功响应数**吗？ | 一致性 |

## C5 · 运维自查

| # | 检查项 |
|---|---|
| 1 | Redis 开了持久化吗？（RDB + AOF） |
| 2 | 设了密码吗？禁用危险命令了吗？ |
| 3 | `maxmemory` 和淘汰策略配了吗？ |
| 4 | 生产用的是**主从 + 哨兵**还是**分片集群**？ |
| 5 | 慢查询日志开了吗？（`slowlog-log-slower-than`） |
| 6 | 有内存和 QPS 监控吗？ |

---

## C6 · 调试工具速查

### Redis 排查

```bash
# 看 key 是否存在 / 类型 / 剩余时间
redis-cli exists 'login:token:xxx'
redis-cli ttl 'login:token:xxx'          # -1 永不过期，-2 不存在
redis-cli type 'cache:shop:1'

# 看 Hash 内容（验登录会话，检查有没有存 password）
redis-cli hgetall 'login:token:xxx'

# 遍历 key（生产用 scan，别用 keys）
redis-cli --scan --pattern 'seckill:*'

# 秒杀数据对账
redis-cli get 'seckill:stock:12'          # Redis 库存
redis-cli scard 'seckill:order:12'        # 已购用户数
redis-cli smembers 'seckill:order:12'     # 已购用户列表

# Stream 队列排查
redis-cli xlen stream.orders              # 队列长度
redis-cli xinfo groups stream.orders      # ★ 消费组信息
redis-cli xpending stream.orders g1       # ★ 未确认消息数
redis-cli xrange stream.orders - +        # 查看所有消息

# 手动建消费组（应急修复）
redis-cli xgroup create stream.orders g1 0 MKSTREAM

# 性能与内存
redis-cli info memory
redis-cli info stats
redis-cli --bigkeys
redis-cli slowlog get 10
```

### JVM 排查

```bash
# 线程栈（找卡住的线程）
jstack <pid> > stack.txt

# 堆内存
jmap -heap <pid>

# GC 情况
jstat -gcutil <pid> 1000
```

**🎯 本项目实战：** 用 `jstack` 抓到后台线程卡在
```
at java.util.concurrent.ArrayBlockingQueue.take(ArrayBlockingQueue.java:420)
at VoucherOrderServiceImpl$VoucherOrderHandler.run(VoucherOrderServiceImpl.java:166)
```
且 `cpu=0.00ms`（说明从启动到现在**一次都没被唤醒**）→ 证实队列里从来没进过东西。

### 日志排查

```powershell
# 统计错误类型和次数（找死循环刷屏）
Get-Content log.txt | Select-String 'ERROR' |
    ForEach-Object { $_.Line -replace '^\d{4}-\d{2}-\d{2}T[\d:.+]+\s+', '' } |
    Group-Object | Sort-Object Count -Descending | Select-Object -First 10

# 找某个异常的完整堆栈
Select-String -Path log.txt -Pattern 'StackOverflowError' -Context 0, 25

# 实时跟踪
Get-Content log.txt -Wait -Tail 50
```

### MySQL 对账

```sql
-- 库存和订单是否一致
SELECT voucher_id, stock FROM tb_seckill_voucher WHERE voucher_id = 12;
SELECT COUNT(*) AS 订单数, COUNT(DISTINCT user_id) AS 用户数
FROM tb_voucher_order WHERE voucher_id = 12;

-- 有没有一个用户多单（一人一单是否失效）
SELECT user_id, COUNT(*) c FROM tb_voucher_order
WHERE voucher_id = 12 GROUP BY user_id HAVING c > 1;

-- 库存有没有被扣成负数（超卖）
SELECT * FROM tb_seckill_voucher WHERE stock < 0;
```

### 请求排查（Apifox / JMeter / 浏览器）

| 症状 | 判断方法 |
|---|---|
| **404** | 路径错（8080 忘了加 `/api`，或券 id 不存在） |
| **401** | 请求头没带上 token（变量未解析 / token 过期 / 头名写错） |
| **405** | HTTP 方法错（**用 GET 打了 POST 接口**） |
| **500 / 服务器异常** | 后端抛异常了，去看后端日志 |

**🎯 本项目所有 POST 接口（其余都是 GET）：**

| 路径 | 方法 |
|---|---|
| `/user/code` | POST |
| `/user/login` | POST |
| `/user/logout` | POST |
| `/user/sign` | POST |
| `/upload/blog` | POST |
| `/voucher/seckill` | POST |
| **`/voucher-order/seckill/{id}`** | **POST** |

**🎯 快速定位技巧（判断请求有没有到后端）：**

```powershell
# 看 nginx 日志里有没有这条请求
Get-Content 'D:\java\Redis\nginx-1.18.0\logs\access.log' -Tail 20 |
    Select-String 'voucher-order'
```

- **nginx 日志里有 → 请求到了网关，问题在后端**
- **nginx 日志里没有 → 请求压根没发出去，问题在客户端配置**

### JMeter 配置自查

| 检查项 | 正确值 |
|---|---|
| 方法 | `POST`（秒杀接口） |
| 端口 | `8081`（直连）或 `8080`（路径需带 `/api`） |
| 请求头名 | `authorization`（小写） |
| 请求头值 | `${token}` 或真实 UUID |
| CSV 元件位置 | 挂在**线程组**下（不是 HTTP 请求下） |
| CSV「首行是表头」 | **勾选** |
| CSV「文件编码」 | `UTF-8`（**不能带空格/Tab**） |
| CSV「结束符再次循环」 | `true` |
| 循环次数 | = token 数量 |

**⚠️ 用返回码反推 `${token}` 有没有解析成功：**

| 返回码 | 含义 |
|---|---|
| **401** | ❌ 变量**没解析**（发出去的是字面量 `${token}`，或 token 已过期） |
| **405** | ✅ 变量解析成功了！只是**方法用了 GET** |
| **200** | ✅ 全部正确 |

**🧠 记忆：401 说明"人和钥匙不匹配"，405 说明"人和钥匙都对，只是走错了门"。**

---

**文档结束**
