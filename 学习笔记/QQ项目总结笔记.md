# QQ 网络通信项目总结笔记

> 基于 TCP Socket + 对象序列化的 C/S 架构即时通讯系统

---

## 一、项目结构总览

```
QQServe（服务端）                    QQclien（客户端）
└── src                              └── src
    ├── First.QQComment                  ├── First.QQComment
    │   ├── Message.java                 │   ├── Message.java
    │   ├── MessageType.java             │   ├── MessageType.java
    │   └── User.java                    │   └── User.java
    ├── QQServer                         ├── First.QQ_View
    │   ├── QQServe.java                 │   └── QQView.java
    │   ├── ServerConnectClientThread    ├── First.Service
    │   ├── ManageServerConnectClient    │   ├── UserClientService.java
    │   └── SendNewsToAll.java           │   ├── ClientConnectServerThread
    └── QQFrame                          │   ├── manageClientConnectServer
        └── QQFrame.java                 │   ├── MessageClientService.java
                                         │   └── FileClientServe.java
                                         └── QQFrame
                                             └── QQFrame.java
```

**核心设计：** 服务端为每个连接的客户端创建一个独立线程，通过 `HashMap<String, 线程>` 管理在线用户。客户端同样为每个连接创建独立线程监听服务端推送。

---

## 二、公共模块（QQComment）

### 1. User —— 用户实体

```java
public class User implements Serializable {
    private String UserID;
    private String Password;
}
```

两端共用的用户模型，实现 `Serializable` 以便在网络中传输。

### 2. Message —— 消息实体

```java
public class Message implements Serializable {
    private String sender;      // 发送者
    private String receiver;    // 接收者（私聊/文件用）
    private String content;     // 文本内容
    private String messageType; // 消息类型（见 MessageType）
    private String Time;        // 发送时间
    private byte[] fileBytes;   // 文件字节
    private int fileLen;        // 文件长度
    private String src;         // 源文件路径
    private String dest;        // 目标文件路径
}
```

**通信原理：** 两端通过 `ObjectOutputStream.writeObject()` / `ObjectInputStream.readObject()` 直接传输 Message 对象，一个 Message 可以涵盖文本、文件、控制指令。

### 3. MessageType —— 消息类型常量

```java
public interface MessageType {
    String MESSAGE_LOGIN_SUCCESS      = "1";  // 登录成功
    String MESSAGE_LOGIN_FAILER       = "2";  // 登录失败
    String MESSAGE_COMMENT_MESSAGE    = "3";  // 私聊
    String MESSAGE_GET_ONLINE_FRIEND  = "4";  // 获取在线列表
    String MESSAGE_RET_ONLINE_FRIEND  = "5";  // 返回在线列表
    String MESSAGE_CLIENT_EXIT        = "6";  // 退出
    String MESSAGE_TO_ALL_MESSAGE     = "7";  // 群发
    String MESSAGE_FILE               = "8";  // 文件传输
}
```

---

## 三、服务端核心（QQServe）

### 1. QQServe —— 服务端入口

**职责：** 监听 9999 端口，接收客户端连接，验证登录，为每个客户端创建通信线程。

```java
public QQServe() {
    // 1. 启动服务端推送线程
    SendNewsToAll sendNewsToAll = new SendNewsToAll();
    new Thread(sendNewsToAll).start();

    // 2. 监听客户端连接
    ss = new ServerSocket(9999);
    while (true) {
        Socket socket = ss.accept();

        // 3. 读取用户登录信息
        ObjectInputStream ois = new ObjectInputStream(socket.getInputStream());
        User u = (User) ois.readObject();
        ObjectOutputStream oos = new ObjectOutputStream(socket.getOutputStream());

        // 4. 验证用户
        if (CheckUser(u.getUserID(), u.getPassword())) {
            // 登录成功 → 创建通信线程
            oos.writeObject(new Message(...MESSAGE_LOGIN_SUCCESS));
            ServerConnectClientThread scct = new ServerConnectClientThread(socket, ...);
            scct.start();
            ManageServerConnectClientThread.addServerConnectClientThread(u.getUserID(), scct);
        } else {
            // 登录失败
            oos.writeObject(new Message(...MESSAGE_LOGIN_FAILER));
            socket.close();
        }
    }
}
```

**用户验证 + 重复登录检测：**

```java
public boolean CheckUser(String UserId, String Password) {
    User user = map.get(UserId);
    if (user == null) return false;
    if (!user.getPassword().equals(Password)) return false;
    // 查在线列表，已登录则拒绝
    if (ManageServerConnectClientThread.getServerConnectClientThread(UserId) != null) {
        return false;
    }
    return true;
}
```

> **关键点：** 使用 `ManageServerConnectClientThread` 判断重复登录，比另建数组更可靠。

### 2. ServerConnectClientThread —— 客户端通信线程

**职责：** 每个客户端一个线程，循环读取客户端发来的消息，按类型分发处理。

```java
public void run() {
    while (true) {
        Message m = (Message) ois.readObject();

        if (m.getMessageType().equals(MESSAGE_GET_ONLINE_FRIEND)) {
            // → 返回在线用户列表
            String list = ManageServerConnectClientThread.GetOnlineUser();
            oos.writeObject(new Message(...MESSAGE_RET_ONLINE_FRIEND, list));

        } else if (m.getMessageType().equals(MESSAGE_CLIENT_EXIT)) {
            // → 客户端退出：从在线列表移除，关闭 socket
            ManageServerConnectClientThread.removeServerConnectClientThread(m.getSender());
            socket.close();
            break;

        } else if (m.getMessageType().equals(MESSAGE_COMMENT_MESSAGE)) {
            // → 私聊：找到接收者的线程，转发消息
            ServerConnectClientThread receiver =
                ManageServerConnectClientThread.getServerConnectClientThread(m.getReceiver());
            if (receiver != null) receiver.getOos().writeObject(m);

        } else if (m.getMessageType().equals(MESSAGE_TO_ALL_MESSAGE)) {
            // → 群发：遍历所有在线线程，发给除自己外的所有人
            HashMap<String, ServerConnectClientThread> map =
                ManageServerConnectClientThread.getMap();
            for (String id : map.keySet()) {
                if (!id.equals(m.getSender())) {
                    map.get(id).getOos().writeObject(m);
                }
            }

        } else if (m.getMessageType().equals(MESSAGE_FILE)) {
            // → 文件传输：找到接收者线程，转发 Message（内含 fileBytes）
            ServerConnectClientThread receiver =
                ManageServerConnectClientThread.getServerConnectClientThread(m.getReceiver());
            if (receiver != null) receiver.getOos().writeObject(m);
        }
    }
}
```

**消息流转图：**

```
客户端A                         服务端                        客户端B
  │                              │                              │
  │── MESSAGE_COMMENT_MESSAGE ──→│                              │
  │                              │── MESSAGE_COMMENT_MESSAGE ──→│
  │                              │  (找到B的线程，转发Message)   │
  │                              │                              │
  │── MESSAGE_TO_ALL_MESSAGE ──→│                              │
  │                              │── MESSAGE_TO_ALL_MESSAGE ──→│
  │                              │  (遍历全部在线线程，跳过自己) │
  │                              │                              │
  │── MESSAGE_FILE ────────────→│                              │
  │                              │── MESSAGE_FILE ────────────→│
  │                              │  (同私聊，直接转发Message)   │
```

### 3. ManageServerConnectClientThread —— 在线用户管理

**职责：** 全局唯一的在线用户 ↔ 线程映射表。

```java
public class ManageServerConnectClientThread {
    private static HashMap<String, ServerConnectClientThread> map = new HashMap<>();

    public static void addServerConnectClientThread(String UserId, ServerConnectClientThread scct) {
        map.put(UserId, scct);
    }
    public static void removeServerConnectClientThread(String UserId) {
        map.remove(UserId);
    }
    public static ServerConnectClientThread getServerConnectClientThread(String UserId) {
        return map.get(UserId);
    }
    public static String GetOnlineUser() {
        // 拼接所有在线用户 ID 为字符串
        return String.join(" ", map.keySet());
    }
}
```

> **为什么关键：** 私聊、群发、重复登录检测、服务端推送，所有功能都依赖这个 Map。

### 4. SendNewsToAll —— 服务端主动推送

**职责：** 独立线程，从控制台读输入，推送给所有在线用户。

```java
public void run() {
    while (true) {
        // 控制台选择 1.推送信息  2.退出
        String news = scanner.nextLine();
        Message message = new Message("服务器", news, ..., MESSAGE_TO_ALL_MESSAGE);

        // 获取在线用户列表，逐个推送
        HashMap<String, ServerConnectClientThread> map =
            ManageServerConnectClientThread.getMap();  // ← 注意：必须用 getMap()
        for (String key : map.keySet()) {
            map.get(key).getOos().writeObject(message);
        }
    }
}
```

---

## 四、客户端核心（QQclien）

### 1. QQView —— 菜单界面

```
=========欢迎登录网络通讯系统=========
         1 登录系统
         9 退出系统

         =====网络通讯系统二级菜单=====
         1 显示在线用户列表
         2 群发信息
         3 私聊信息
         4 发送文件
         9 退出系统
```

控制台驱动的菜单循环，通过 `UserClientService`、`MessageClientService`、`FileClientServe` 调用后端逻辑。

### 2. UserClientService —— 登录与连接

```java
public boolean checkUser(String UserId, String Password) {
    // 1. 连接服务端 9999
    socket = new Socket(InetAddress.getLocalHost(), 9999);
    oos = new ObjectOutputStream(socket.getOutputStream());

    // 2. 发送 User 对象
    oos.writeObject(new User(UserId, Password));

    // 3. 读取登录结果
    ois = new ObjectInputStream(socket.getInputStream());
    Message message = (Message) ois.readObject();

    if (message.getMessageType().equals(MESSAGE_LOGIN_SUCCESS)) {
        // 4. 启动监听线程（持续接收服务端推送）
        ClientConnectServerThread ccst = new ClientConnectServerThread(socket, ois, oos);
        ccst.start();
        manageClientConnectServerThread.addClientConnectServerThread(UserId, ccst);
        return true;
    } else {
        socket.close();
        return false;
    }
}
```

**登录交互流程：**

```
客户端                          服务端
  │                              │
  │──── User对象 ──────────────→│  ObjectOutputStream.writeObject()
  │                              │
  │←─── Message(登录成功/失败) ──│  ObjectOutputStream.writeObject()
  │                              │
  │   (成功) 启动监听线程         │  启动 ServerConnectClientThread
  │   等待服务端推送              │
```

### 3. ClientConnectServerThread —— 客户端监听线程

**职责：** 登录成功后启动，循环读取服务端发来的所有消息（私聊、群发、在线列表、文件），按类型处理。

```java
public void run() {
    while (true) {
        Message message = (Message) ois.readObject();

        if (message.getType().equals(MESSAGE_RET_ONLINE_FRIEND)) {
            // 显示在线用户列表
            String[] users = message.getContent().split(" ");
            for (String user : users) System.out.println("用户：" + user);

        } else if (message.getType().equals(MESSAGE_COMMENT_MESSAGE)) {
            // 收到私聊
            System.out.println(message.getSender() + " 对我说：" + message.getContent());

        } else if (message.getType().equals(MESSAGE_TO_ALL_MESSAGE)) {
            // 收到群发
            System.out.println(message.getSender() + " 对大家说：" + message.getContent());

        } else if (message.getType().equals(MESSAGE_FILE)) {
            // 收到文件
            FileOutputStream fos = new FileOutputStream(message.getDest());
            fos.write(message.getFileBytes());
            fos.close();
            System.out.println("收到文件，保存成功");
        }
    }
}
```

### 4. manageClientConnectServerThread —— 客户端线程管理

```java
public class manageClientConnectServerThread {
    private static HashMap<String, ClientConnectServerThread> hm = new HashMap<>();

    public static void addClientConnectServerThread(String UserId, ClientConnectServerThread ccst) {
        hm.put(UserId, ccst);
    }
    public static ClientConnectServerThread getClientConnectServerThread(String UserId) {
        return hm.get(UserId);
    }
}
```

### 5. MessageClientService —— 发送消息

```java
// 群发
public void sendMessageToAll(String senderID, String content) {
    Message message = new Message(senderID, content, ..., MESSAGE_TO_ALL_MESSAGE);
    ObjectOutputStream oos = manageClientConnectServerThread
        .getClientConnectServerThread(senderID).getOutputStream();
    oos.writeObject(message);
}

// 私聊
public void sendMessage(String content, String sendID, String getterId) {
    Message message = new Message(sendID, getterId, content, ..., MESSAGE_COMMENT_MESSAGE);
    ObjectOutputStream oos = manageClientConnectServerThread
        .getClientConnectServerThread(sendID).getOutputStream();
    oos.writeObject(message);
}
```

### 6. FileClientServe —— 发送文件

```java
public void SendFile(String src, String dest, String Sender, String receive) {
    // 1. 读取本地文件到字节数组
    FileInputStream fis = new FileInputStream(src);
    byte[] buffer = new byte[(int) new File(src).length()];
    fis.read(buffer);

    // 2. 封装到 Message
    Message message = new Message(Sender, receive, ..., MESSAGE_FILE);
    message.setFileBytes(buffer);
    message.setSrc(src);
    message.setDest(dest);

    // 3. 发送
    ObjectOutputStream oos = manageClientConnectServerThread
        .getClientConnectServerThread(Sender).getOutputStream();
    oos.writeObject(message);
}
```

---

## 五、启动入口

**服务端：** `QQFrame.main()` → `new QQServe()`
```java
public class QQFrame {
    public static void main(String[] args) {
        new QQServe();  // 构造方法里启动 server socket + 推送线程
    }
}
```

**客户端：** `QQFrame.main()` → `new QQView().MainMenu()`
```java
public class QQFrame {
    public static void main(String[] args) {
        new QQView().MainMenu();  // 控制台菜单循环
    }
}
```

---

## 六、完整通信流程总结

```
┌─────────────────────────────────────────────────────────────────┐
│                        QQ 通信架构总览                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  客户端A          服务端 (9999)             客户端B              │
│    │                  │                       │                 │
│    │── 1.登录 ──────→│                       │                 │
│    │←── 登录成功 ────│                       │                 │
│    │                  │── 创建线程管理 ──────│                 │
│    │                  │                       │                 │
│    │── 2.私聊 ──────→│── 转发 Message ─────→│                 │
│    │                  │                       │                 │
│    │── 3.群发 ──────→│── 转发给全部在线 ───→│                 │
│    │                  │                       │                 │
│    │── 4.发文件 ────→│── 转发 Message ─────→│                 │
│    │                  │                       │                 │
│    │←── 5.服务端推送 ─│                       │                 │
│    │                  │                       │                 │
│    │── 6.退出 ──────→│── 移除在线列表        │                 │
│    │                  │                       │                 │
└─────────────────────────────────────────────────────────────────┘
```

| 步骤 | 操作 | 客户端发出 | 服务端处理 | 目标接收 |
|------|------|-----------|-----------|---------|
| 1 | 登录 | `User` 对象 | `CheckUser()` → 创建线程 | 返回登录结果 |
| 2 | 私聊 | `MESSAGE_COMMENT_MESSAGE` | 查目标线程 → 转发 | 目标客户端 |
| 3 | 群发 | `MESSAGE_TO_ALL_MESSAGE` | 遍历在线 Map → 逐条转发 | 所有在线（排除自己） |
| 4 | 文件 | `MESSAGE_FILE`（含字节数组） | 查目标线程 → 转发 Message | 目标客户端 |
| 5 | 推送 | 服务端控制台触发 | 遍历在线 Map → 逐条转发 | 所有在线 |
| 6 | 退出 | `MESSAGE_CLIENT_EXIT` | `removeServerConnectClientThread()` | — |

---

## 七、关键设计要点

1. **一连接一线程**：每个客户端对应一个 `ServerConnectClientThread`，在 `while(true)` 里阻塞读消息
2. **全局 Map 管理在线状态**：`ManageServerConnectClientThread.map` 是整个系统的在线状态中心
3. **Message 即协议**：所有通信内容封装进 `Message` 对象，靠 `messageType` 区分类型
4. **ObjectStream 序列化通信**：直接传 Java 对象，无需手动解析 JSON/XML
5. **服务端即转发器**：服务端不做消息存储，纯粹做消息路由（私聊→目标，群发→全部）

## 八、常见踩坑记录

| 问题 | 原因 | 解决 |
|------|------|------|
| 服务端推送不生效 | `SendNewsToAll` 里用了 `new HashMap<>()` 而不是 `getMap()` | 改为 `ManageServerConnectClientThread.getMap()` |
| 重复登录检测失效 | 静态变量初始化顺序导致 `IsLogin` 数组为空 | 改用 `ManageServerConnectClientThread` 检测 |
| 客户端强关导致无法重登 | 未在 `IOException` 中清理在线列表 | 在 `catch` 里加 `removeServerConnectClientThread()` |
