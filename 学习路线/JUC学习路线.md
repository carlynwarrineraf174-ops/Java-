# JUC 并发编程 · 学习路线（2026.11 中 → 12 月）

> **定位**：`技术栈规划-2026.10至2027.02.md` 里的**第一优先级技术栈**，本文件是它的执行版。
> **前提**：国庆前学完 Spring Cloud + MQ ✅　|　**每日投入**：6 h（在校也能稳定保证）
> **目标**：做到「**能讲原理 + 能画图 + 能手写**」，直接支撑 2027.02 面试
>
> 🔄 **改期说明（2026.09.26 二次修订）**：原定"10 月主线、3 周拿下"，
> 现改为 **11 月初并行启动（每天固定 1.5 h）+ 12 月收尾**（10–11 月主线让给 MQ 全量跟完和 AI 线）。
> **🔄 四次修订（2026.10.04）**：改为**串行执行**（见 `技术栈规划` §0.5.8）—— JUC 段 = **10.21–11.05**，每天主线 4.5 h（**不再是 1.5 h 副线**），12 月的硬 deadline 不变。
> **🔄 三次修订（2026.10.02）**：① 启动时间由「11 月中」**提前到 11 月初**（AI 线入门只需 ≈30 h，10 月底即可结束）；② 采用 **30 h「实习过关版」**（见 §0.1），砍掉 3 块——**够实习面试，不够大厂 SP/SSP 追问**；③ 计网已完成、1 月释放 35 h，但**本路线的 12 月硬 deadline 不变**。
> 下面第 5 节的**三周排期仍然有效**，只是从"连续 3 周 × 3~4 h"改成"**约 6 周 × 1.5 h**"：
> 把表格里的"日期"读成"第 1~3 天 / 第 4~5 天…"，每格拆到 2~3 天完成即可。
> ⚠️ **硬 deadline：12 月内必须收尾**，否则会挤掉 1 月的 MySQL / 计网 / OS。

---

## 0. 速览表（先看这张）

| # | 知识块 | 耗时 | 面试复现率 | 等级 | 必须产出 |
|---|--------|------|-----------|------|---------|
| 1 | 并发基础 + JMM + volatile | 5 h | ★★★★★ | 🔥必学 | 能讲 happens-before 三大规则 |
| 2 | synchronized + 锁升级 + 对象头 | 5 h | ★★★★★ | 🔥必学 | 能画 Mark Word 与锁升级路径 |
| 3 | CAS + 原子类 + Unsafe | 4 h | ★★★★☆ | 🔥必学 | 能讲 ABA 与 LongAdder 分段 |
| 4 | **AQS 原理（源码级）** | 10 h | ★★★★★ | 🔥**核心** | 能白板画 CLH 队列 + 独占/共享 |
| 5 | ReentrantLock / 读写锁 / Condition | 5 h | ★★★★☆ | 🔥必学 | 能讲公平锁与非公平锁差异 |
| 6 | **线程池（源码级）** | 8 h | ★★★★★ | 🔥**核心** | 能画 execute 流程图 + 手写线程池 |
| 7 | 并发容器（CHM 源码 / COW / 阻塞队列） | 6 h | ★★★★★ | 🔥必学 | 能讲 JDK7 vs JDK8 的 CHM |
| 8 | 同步工具类（Latch/Barrier/Semaphore） | 2 h | ★★★☆☆ | 🔥必学 | 能说清三者区别与场景 |
| 9 | **CompletableFuture 异步编排** | 4 h | ★★★★☆ | 🔥必学 | 能用它优化一个真实接口 |
| 10 | ThreadLocal（原理 + 泄漏 + 跨线程） | 3 h | ★★★★★ | 🔥必学 | 能讲 ThreadLocalMap 与弱引用 |
| 11 | 线程基础与生命周期 | 2 h | ★★★☆☆ | ⚡快速过 | 6 状态转换图 |
| 12 | 并发设计模式 + 实战手写 | 6 h | ★★★★☆ | 🔥必学 | 8 道手写题全部独立完成 |
| — | ForkJoin / 并行流 | 2 h | ★★☆☆☆ | 👀选看 | 知道适用场景即可 |
| — | 无锁编程 / Disruptor / 协程（Loom） | 2 h | ★☆☆☆☆ | ⏸跳过 | 面试几乎不问 |

**合计 ≈ 58 h**（比规划的 40 h 多，因为按 6 h/天把 AQS 和线程池做到了源码级）

### 0.1 「30 h 实习过关版」取舍（2026.10.02 三次修订）

时间紧时按这个版本走：**能答第二层，不追求源码级与手写**。

| 块 | SP 版 | 过关版 | 怎么砍 |
|---|------|--------|--------|
| 4 · AQS 原理（源码级） | 10 h | **4 h** | 只求讲清「CLH 队列 + state + 独占/共享」，**不白板画源码** |
| 6 · 线程池（源码级） | 8 h | **4 h** | 只求画 execute 流程图 + 讲 7 大参数，**不手写线程池** |
| 12 · 并发设计模式 + 实战手写 | 6 h | **1 h** | 8 道手写题降为「看得懂」，只留 DCL 单例 + 生产者消费者 |
| 其余 9 块 | 34 h | 21 h | 内容不变，压缩示例与源码跟读 |
| **合计** | **58 h** | **≈30 h** | **省 28 h** |

> ⚠️ **代价**：大厂 SP/SSP 会问「手写线程池」「AQS 怎么实现的」，过关版答不到第三层。
> 换来的是 28 h → 投给**高并发改造**（面试数字）或**日常实习投递**。**这是取舍，不是省事。**

---

## 1. 为什么 JUC 排第一（性价比论证）

| 理由 | 说明 |
|------|------|
| **复现率最高** | Java 后端面试里，并发几乎是**开场必问**：线程池参数、AQS、volatile、CHM、ThreadLocal 五件套 |
| **你已有基础，学习成本低一半** | 你**已经用过**：`ThreadLocal`（苍穹外卖 `BaseContext`）、线程池（黑马点评异步秒杀）、`synchronized`（韩顺平作业抢红包）、`FutureTask`（Callable 作业）、Redisson（可重入锁/看门狗）。学 JUC = 把"用过的东西"升级成"讲得出原理" |
| **能反向加固你的项目** | 学完你会立刻发现：原来苍穹外卖的 ThreadLocal 没 remove 是隐患、黑马点评的 Redisson 看门狗就是 AQS + 定时任务、秒杀异步下单就是线程池 + 阻塞队列 |
| **它是 JVM 的前置** | 线程、锁、对象头、CAS 是 JVM 锁优化/逃逸分析的基础 |

---

## 2. 逐块详解

### 块 1 · 并发基础 + JMM + volatile　5 h　🔥必学

**要回答的核心问题**：为什么会有并发问题？`volatile` 到底解决了什么？

- **三大特性**：原子性 / 可见性 / 有序性 —— 分别由谁保证（锁 / volatile / happens-before）
- **JMM**：主内存与工作内存、8 种原子操作（了解即可）、**happens-before 三大常用规则**（程序顺序、监视器锁、volatile 变量规则）
- **重排序**：编译器/CPU/内存三级重排、内存屏障（LoadLoad/StoreStore/LoadStore/StoreLoad）
- **volatile**：可见性 + 禁止重排（**不保证原子性**！）、`i++` 为什么线程不安全
- **经典手写**：DCL 单例为什么要加 volatile（防"半初始化对象"被发布）

**验收**：能用 DCL 单例解释 volatile 的必要性；能说清"volatile 不能保证 i++ 安全"。

### 块 2 · synchronized + 锁升级 + 对象头　5 h　🔥必学

- **用法**：同步代码块（锁对象）/ 实例方法（this）/ 静态方法（Class）
- **对象头**：Mark Word 结构（hashcode / 分代年龄 / 锁标志位）、`jol-core` 打印对象头（**动手做一次**）
- **锁升级**：无锁 → 偏向锁 → 轻量级锁（CAS 自旋）→ 重量级锁（monitor / ObjectMonitor）
  > ⚠️ **版本差异（加分细节）**：JDK 15 起**偏向锁默认关闭并已废弃**（JEP 374）。面试谈锁升级时补一句"这是历史实现，新版本默认关闭"，会显得你读过真实资料而不是背八股。
- **锁优化**：锁消除（逃逸分析）、锁粗化、自适应自旋
- **synchronized vs ReentrantLock**：实现层次（JVM vs API）、可中断/超时/公平、条件队列数量

**必画**：Mark Word 结构图 + 锁升级路径图。

### 块 3 · CAS + 原子类 + Unsafe　4 h　🔥必学

- **CAS 原理**：`Unsafe.compareAndSwapInt` → CPU `cmpxchg` 指令 + 缓存一致性协议
- **三大问题**：**ABA**（`AtomicStampedReference` 解决）、自旋开销大、只能保证一个变量
- **原子类家族**：基本类型、数组、引用、字段更新器（`AtomicIntegerFieldUpdater`）
- **LongAdder vs AtomicLong**：分段 Cell 减少竞争（**能讲清"为什么高并发下 LongAdder 更快"**）
- **实战**：用 `AtomicInteger` 和 `LongAdder` 各写一个并发计数器，压测对比

**验收**：能说清 `AtomicInteger` 与 `LongAdder` 的取舍（精确值 vs 高吞吐）。

### 块 4 · AQS 原理（源码级）　10 h　🔥**核心中的核心**

> AQS 是 JUC 的地基：`ReentrantLock`、`ReadWriteLock`、`Semaphore`、`CountDownLatch`、`ThreadPoolExecutor.Worker` 全建立在它之上。

- **结构**：`volatile int state` + **CLH 双向队列**（head/tail）+ `Node` 的 `waitStatus`
- **独占模式**：`acquire` → `tryAcquire` → `addWaiter` → `acquireQueued` → 唤醒
- **共享模式**：`acquireShared` / `releaseShared`，`PROPAGATE` 状态的作用
- **模板方法模式**：AQS 定流程，子类只实现 `tryAcquire` / `tryRelease`
- **条件队列**：`ConditionObject` 的 `await` / `signal`（await 会**释放锁并进入条件队列**，signal 后转移到同步队列）
- **源码跟读**：在 IDE 里给 `ReentrantLock.lock()` 打断点，一步步走完"获取不到锁 → 入队 → 挂起 → 被唤醒"的全过程（**这一步是分水岭**）

**必画**：CLH 队列 + state 状态变化图（至少画 3 个线程竞争的场景）。
**验收**：能把 `ReentrantLock.lock()` 的执行路径讲到 `LockSupport.park()` 那一层。

### 块 5 · ReentrantLock / 读写锁 / Condition　5 h　🔥必学

- **可重入**：state 计数（同一线程重入 +1，释放 -1 到 0）
- **公平锁 vs 非公平锁**：`hasQueuedPredecessors()` 的差别、为什么非公平吞吐更高
- **`lockInterruptibly` / `tryLock(timeout)`**：synchronized 做不到的能力
- **`ReentrantReadWriteLock`**：读写互斥、写写互斥、**读读并发**；锁降级（写→读）可行，锁升级不可行
- **StampedLock**（选看）：乐观读
- **Condition**：用两个 Condition 实现**生产者消费者**（对比 `wait/notify`：能精准唤醒）

**手写**：用 ReentrantLock + 2 个 Condition 实现阻塞队列。

### 块 6 · 线程池（源码级）　8 h　🔥**核心（面试最常问）**

- **7 大参数**：corePoolSize / maximumPoolSize / keepAliveTime / unit / workQueue / threadFactory / handler
- **execute 流程（必背 + 必画）**：
```
提交任务 → 核心线程未满? 新建核心线程
        → 队列未满? 入队
        → 线程数 < max? 新建非核心线程
        → 全部满 → 拒绝策略
```
- **线程复用原理**：`runWorker()` 的 while 循环 + `getTask()` 阻塞在队列上
- **4 种拒绝策略**：Abort（默认，抛异常）/ CallerRuns / Discard / DiscardOldest
- **阻塞队列选择**：`ArrayBlockingQueue` / `LinkedBlockingQueue`（默认无界→**可能 OOM**）/ `SynchronousQueue` / `PriorityBlockingQueue` / `DelayedWorkQueue`
- **参数怎么配**：CPU 密集 N+1、IO 密集 2N（要能说出"这是经验值，真实要靠压测 + 监控动态调"）
- **`submit` vs `execute`**：返回值与异常吞掉的坑
- **`ScheduledThreadPoolExecutor` / `ForkJoinPool`**：了解 + 会用
- **动态调参**：`setCorePoolSize` / 自定义队列（选看，加分）
- **`Executors` 为什么被阿里规约禁用**：FixedThreadPool 无界队列 OOM、CachedThreadPool 无限线程 OOM → **直接用 `ThreadPoolExecutor` 构造**

**手写**：一个简易线程池（任务队列 + Worker 线程 + 拒绝策略），约 80 行。
**验收**：能白板画 execute 流程图 + 说出"为什么阿里禁止用 Executors"。

### 块 7 · 并发容器　6 h　🔥必学

- **`ConcurrentHashMap`（重点）**：
  - JDK7：Segment 分段锁 + ReentrantLock
  - **JDK8**：`Node[]` + CAS + `synchronized` 锁头节点 + 红黑树（链表 ≥ 8 且数组 ≥ 64 才树化）
  - `size()` 怎么实现（baseCount + CounterCell 分段计数）
  - `put` 的并发细节（`tabAt`/`casTabAt`/`helpTransfer`）
  - 为什么不允许 null key/value（并发下歧义）
- **`CopyOnWriteArrayList`**：写时复制、适用"读多写极少"、内存与一致性代价
- **阻塞队列家族**：`ArrayBlockingQueue` / `LinkedBlockingQueue` / `SynchronousQueue` / `PriorityBlockingQueue` / `DelayQueue` / `LinkedTransferQueue`（能说出各自场景）
- **`ConcurrentLinkedQueue`**：CAS 无锁队列

**验收**：能讲清 JDK8 CHM 的"CAS + synchronized + 红黑树"三段式，以及为什么放弃分段锁。

### 块 8 · 同步工具类　2 h　🔥必学

| 类 | 作用 | 典型场景 |
|---|---|---|
| `CountDownLatch` | 一次性倒计时（不可重置） | 主线程等多个子任务完成 |
| `CyclicBarrier` | 可循环的屏障（可重置） | 多线程分阶段对齐 |
| `Semaphore` | 许可数（限流） | 接口限流、连接池 |

**验收**：能一句话说清 Latch 与 Barrier 的区别（**能否重用**）。

### 块 9 · CompletableFuture 异步编排　4 h　🔥必学（实战最有用）

- `supplyAsync` / `runAsync` / 自定义线程池（**必须传自己的池，否则用 ForkJoinPool.commonPool 会互相影响**）
- 编排：`thenApply` / `thenCompose` / `thenCombine` / `allOf` / `anyOf` / `exceptionally` / `handle` / `whenComplete`
- **实战**：把你项目里一个"串行调 3 个服务"的接口改成并行聚合（比如商品详情 = 商品 + 库存 + 评价），**用 JMeter 测出耗时下降数据**（面试可讲）
- 常见坑：默认线程池、异常处理、超时控制（`orTimeout`）

**验收**：能把一个真实接口改成并行编排并给出优化前后耗时。

### 块 10 · ThreadLocal　3 h　🔥必学（你项目里正在用）

- **原理**：`Thread` 持有 `ThreadLocalMap`，key 是 ThreadLocal（**弱引用**），value 强引用
- **内存泄漏**：key 被回收后 value 还在 → 必须 `remove()`（**呼应你苍穹外卖里发现的问题：拦截器没 remove**）
- **哈希冲突**：开放定址法（线性探测）而非链表
- **跨线程传递**：`InheritableThreadLocal`（父子线程）、**`TransmittableThreadLocal`**（线程池场景，阿里开源）
- **实战回看**：把你项目里 `BaseContext` 的用法 + 缺失的 `remove()` 写成一篇笔记

### 块 11 · 线程基础与生命周期　2 h　⚡快速过

6 种状态与转换（NEW / RUNNABLE / BLOCKED / WAITING / TIMED_WAITING / TERMINATED）、`sleep` vs `wait`（**是否释放锁**）、`join`、`yield`、线程中断机制（`interrupt` + `isInterrupted`，别用 `stop`）、守护线程。

### 块 12 · 并发设计模式 + 实战手写　6 h　🔥必学

- 生产者消费者（三种实现：`wait/notify`、`BlockingQueue`、`Lock+Condition`）
- 两线程交替打印（1-100 奇偶、ABC 循环）
- 单例（DCL + volatile / 静态内部类 / 枚举）
- 限流器（`Semaphore` 或令牌桶）
- 多线程分片处理大任务 + 结果汇总

---

## 3. 手写清单（8 题，必须自己敲出来，不许抄）

| # | 题目 | 检验点 |
|---|------|--------|
| 1 | DCL 单例（含 volatile 与为什么） | 理解重排序与对象发布 |
| 2 | 生产者消费者（wait/notify 版 + BlockingQueue 版 + Condition 版） | 三种线程通信方式 |
| 3 | 两线程交替打印 1~100 | 精确唤醒 |
| 4 | 三线程循环打印 ABC | Condition / Semaphore |
| 5 | **简易线程池**（任务队列 + Worker + 拒绝策略） | 线程复用原理 |
| 6 | 用 `ReentrantLock` + Condition 实现阻塞队列 | AQS 应用 |
| 7 | `CompletableFuture` 并行调用 3 个服务并聚合 | 异步编排 |
| 8 | 用 `Semaphore` 实现一个简单限流器 | 并发工具类实战 |

> 📁 代码放 `D:\java\JavaSE\06-多线程与线程池\` 下新建 `juc-practice\`，每个文件配注释说明思路。

---

## 4. 面试题清单（30 问，按"必须秒答 / 要能展开 / 加分"分级）

**必须秒答（15 问）**
1. volatile 的作用与原理？为什么不保证原子性？
2. synchronized 和 ReentrantLock 的区别？
3. synchronized 的锁升级过程？
4. CAS 是什么？有什么问题？怎么解决 ABA？
5. 线程池的 7 个参数？
6. 线程池的执行流程？
7. 线程池的 4 种拒绝策略？
8. 核心线程数怎么配？为什么？
9. 为什么阿里规约禁止用 `Executors`？
10. `ConcurrentHashMap` 在 JDK8 为什么放弃分段锁？
11. `ThreadLocal` 的原理？为什么会内存泄漏？怎么避免？
12. `sleep` 和 `wait` 的区别？
13. `CountDownLatch` 和 `CyclicBarrier` 的区别？
14. 什么是死锁？四个必要条件？怎么排查（jstack）？
15. `volatile` 和 `synchronized` 的区别？

**要能展开（10 问）**
16. AQS 的原理？独占和共享模式的区别？
17. 公平锁和非公平锁的实现差异？为什么非公平吞吐更高？
18. `ReentrantLock` 的 `lock()` 到 `park()` 的完整路径？
19. `Condition` 的 `await/signal` 原理？和 `wait/notify` 的区别？
20. 线程池的线程复用是怎么实现的（`runWorker`）？
21. `LinkedBlockingQueue` 做线程池队列有什么风险？
22. `LongAdder` 为什么比 `AtomicLong` 快？
23. `ConcurrentHashMap` 的 `size()` 怎么统计的？
24. `CopyOnWriteArrayList` 的适用场景和代价？
25. `submit` 和 `execute` 的区别？异常怎么处理？

**加分（5 问）**
26. 偏向锁在新版本为什么被废弃？
27. `TransmittableThreadLocal` 解决了什么问题？
28. 怎么实现一个动态调参的线程池？监控哪些指标？
29. 线程池里的任务抛异常会怎样？线程会死吗？
30. 你项目里哪里用了并发？踩过什么坑？（**结合苍穹外卖 ThreadLocal + 黑马点评秒杀讲**）

---

## 5. 排期表（原「三周 × 6 h」版，现按 **11 月初启动 · 每天 1.5 h · 12 月收尾** 拉伸执行）

| 日期 | JUC 主线（3~4 h） | 其余时间（2~3 h） | 当日产出 |
|------|------------------|------------------|---------|
| 10/1–10/2 | 块1 JMM/volatile + 块2 synchronized 锁升级 | 算法 DP 2 题 | 对象头笔记 + 锁升级图 |
| 10/3–10/4 | 块3 CAS/原子类 + 手写题 1、8 | 算法 DP 2 题 | 原子类对比压测数据 |
| 10/5–10/7 | 块4 AQS 源码（独占 + 共享 + Condition） | 算法 2 题 + 博客 1 篇 | **AQS 手画图** |
| 10/8–10/9 | 块5 ReentrantLock/读写锁 + 手写题 2、6 | 微服务项目推进 | 阻塞队列手写 |
| 10/10–10/12 | 块6 线程池源码 + 手写题 5 | 算法 2 题 + 项目 | **简易线程池 + execute 流程图** |
| 10/13–10/14 | 块7 并发容器（CHM 源码重点） | 微服务项目 | CHM 结构对比笔记 |
| 10/15 | 块8 工具类 + 块11 线程基础 | 算法 2 题 | 6 状态转换图 |
| 10/16–10/17 | 块9 CompletableFuture + **优化一个真实接口** | 项目压测 | 接口优化前后耗时 |
| 10/18 | 块10 ThreadLocal + 回看你项目里没 remove 的坑 | 博客 1 篇 | ThreadLocal 笔记 |
| 10/19 | 块12 并发设计模式 + 手写题 3、4、7 | — | 手写清单全绿 |
| 10/20 | 面试 30 问自测 + 错题复盘 | 下阶段（JVM）预习 | **JUC 自测通过** |

> 📌 每天剩下的时间给：**算法（DP/HOT100）+ 微服务项目落地**——三件事并行，但**JUC 是唯一的主线**，不许同时开第二门新课。

---

## 6. 资源与用法（别贪多，各取一个）

| 类型 | 推荐 | 怎么用 |
|------|------|--------|
| **书（主）** | 《Java 并发编程的艺术》（方腾飞） | 薄、面试向，AQS/线程池/CHM 三章精读 |
| 书（补） | 《Java 并发编程实战》（JCIP） | 只挑"取消与关闭""线程池"两章，别从头啃 |
| 视频 | B站搜「黑马 JUC」「尚硅谷 JUC」 | 只用来**预习概念**，1.5x；AQS 和线程池必须自己读源码，视频讲不透 |
| **源码（关键）** | `AQS`、`ReentrantLock`、`ThreadPoolExecutor`、`ConcurrentHashMap`、`ThreadLocal` | 在 IDE 里打断点单步跟，比看 10 篇文章都值 |
| 工具 | `jol-core`（对象头）、`jstack`、`Arthas`、JMeter | 做实验：打印对象头、dump 线程、压测计数器 |
| 文章 | [2026 后端八股文学习路线](https://developer.cloud.tencent.cn/article/2658484)、美团技术团队 AQS/线程池系列 | 当"查漏清单"用，不要当教材 |

---

## 7. 验收标准（三级，缺一不算学会）

> 📌 通用标准见 **`学习方法与面试呈现SOP.md` 第 2 节**：学完一块要交 **图 + demo + 10 张问答卡 + 博客 + 简历话术**。
> 下面是 JUC 的专项验收。

| 级别 | 标准 | 自测方式 |
|------|------|---------|
| **会讲** | 30 问里 25 问能连续讲 1 分钟不打结 | 录音，回放听有没有"嗯…那个…" |
| **会画** | 线程池 execute 流程图 / AQS CLH 队列 / 锁升级路径 / CHM 结构 / 6 状态转换，**5 张图白板 3 分钟画完** | 拿白纸默画，画完对照笔记 |
| **会写** | 手写清单 8 题独立完成（不看资料），并能解释每行为什么这么写 | 新建空文件重写一遍 |

**JUC 的 10 张问答卡（按这个顺序写）**：① 线程池参数怎么定 ② 线程池执行流程 ③ 为什么禁用 Executors
④ AQS 原理 ⑤ 公平锁 vs 非公平锁 ⑥ volatile 为什么不保证原子性 ⑦ CAS 与 ABA ⑧ CHM 为什么放弃分段锁
⑨ ThreadLocal 内存泄漏 ⑩ CompletableFuture 并行编排（配你项目的耗时数据）。

---

## 8. 和你已有项目的结合点（学完立刻回看，印象翻倍）

| 你项目里的东西 | 对应 JUC 知识 | 回看会发现什么 |
|--------------|--------------|---------------|
| 苍穹外卖 `BaseContext.getCurrentId()` | ThreadLocal 原理 | **拦截器没 remove** → 线程池复用会串号（P1 隐患） |
| 苍穹外卖 `@Async` 相关（如果加） | 事务不跨线程 | 为什么异步方法里 `@Transactional` 失效 |
| 黑马点评秒杀异步下单 | 线程池 + 阻塞队列 | `orderTasks` 的消费线程 = 单线程 Executor；用 Stream 做队列 |
| 黑马点评 Redisson 看门狗 | AQS + 定时续期 | 看门狗本质是 `ScheduledExecutorService` 定时任务 |
| 黑马点评 Redisson 可重入锁 | AQS state 计数 | Hash 里的重入次数 = AQS 的 state |
| 微服务 OpenFeign 调用多个服务 | CompletableFuture | 可以改造成并行编排降低 RT |
| 微服务 Sentinel 限流 | Semaphore / 令牌桶 | 滑动窗口 vs 漏桶 vs 令牌桶 |

> 📌 **动作**：JUC 学完后，回头把"苍穹外卖 ThreadLocal 未清理"和"黑马点评看门狗/可重入锁"各写成一篇笔记 —— 这三篇就是面试时"我真的懂并发"的证据。

---

## 9. 常见误区（避开这些就赢一半）

1. **只背八股不写代码** → 手写清单 8 题一个都不能少
2. **跳过 AQS 直接背线程池** → AQS 是地基，跳过它线程池/锁都讲不深
3. **把 volatile 当万能（以为保证原子性）** → `i++` 反例必须能举
4. **不区分 JDK 版本** → CHM 的 JDK7/8 差异、偏向锁的废弃，都是加分点
5. **线程池参数死记"N+1 / 2N"** → 要补"经验值 + 压测 + 监控动态调整"
6. **只看博客不看源码** → 至少 AQS/线程池/CHM 三个要自己跟一遍
7. **忽略 CompletableFuture** → 这是**唯一能直接变成项目亮点**的一块（接口并行优化）
8. **学完不复习** → 30 问要在 10/20、11 月底、2 月面试前各过一遍（间隔复习）

---

## 10. 学完之后（衔接）

- **下一站**：`JVM`（12 月，采用 30 h 过关版，见 `技术栈规划` §0.5.6）—— 锁优化、逃逸分析、GC 与线程的关系都建立在 JUC 之上
- **同步要做的**：
  - 算法：DP 15 题（10 月）+ HOT100 启动
  - 项目：微服务商城落地 + 把黑马点评改造成"可压测版本"
  - 输出：3 篇博客（AQS 手画图 / 线程池源码 / 我项目里的并发坑）

> 🎯 **一句话**：JUC 是"投入 58 h，面试至少 5 个问题能答到第二层"的技术栈 —— 在所有候选里性价比最高，所以排第一（**58 h 为 SP 版，30 h 为过关版，见 §0.1**）。
