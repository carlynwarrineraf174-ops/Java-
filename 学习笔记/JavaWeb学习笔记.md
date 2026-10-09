# JavaWeb 学习笔记（全量版）

> 依据黑马「JavaWeb」20 篇讲义整理，笔记风格与《Java学习笔记.md》一致。
> 配套路线：学完本笔记即可衔接 SSM / Spring Boot 项目实战（如苍穹外卖）。

---

## ★ 重要性总览（学之前先看这个）

### 🔴 非常重要（面试+项目核心，务必吃透，重点掌握）
| 知识点 | 章节 | 为什么重要 |
|--------|------|-----------|
| SpringBoot 入门 + 三层架构 | 06 | 整个框架开发的基础骨架 |
| IOC 控制反转 + DI 依赖注入 | 06 | Spring 第一大核心，必考必用 |
| MyBatis（注解+XML+动态SQL） | 07 | 持久层主流框架 |
| 事务管理 @Transactional | 08 | 保证数据一致性，必考 |
| 登录认证 JWT + Filter/Interceptor | 09 | 前后端分离认证主流方案 |
| AOP 面向切面 | 10 | Spring 第二大核心 |
| HTTP 协议 | 05 | 理解请求/响应一切的基础 |

### 🟡 重要（面试/项目常考，需掌握）
| 知识点 | 章节 |
|--------|------|
| JavaScript 基础 + Vue 指令 + Axios | 02/03 |
| 多表查询（JOIN/子查询）+ 外键 | 07 |
| 参数接收（@RequestBody/@RequestParam/@PathVariable） | 08 |
| 全局异常处理器 | 08 |
| RESTful 风格 + 统一响应 Result | 08 |
| 分页查询（PageHelper） | 07 |
| Maven 依赖管理 | 04 |
| 项目部署（Linux + Docker） | 11 |

> 📌 **学完 06~08 后务必读「12 · 项目实战对照」**：用真实"泰乐员工管理系统"代码把理论串起来，面试能讲出真实落地写法。

### 🟢 不重要（会用即可/了解，一笔带过）
| 知识点 | 说明 |
|--------|------|
| HTML+CSS 标签细节 | 用 AI 生成，看懂即可，不深究 |
| ElementPlus 组件复制 | 查官网拷贝改改，不背 |
| 阿里云 OSS 具体配置 | 用到再查文档，SDK 思路通用 |
| Maven 安装配置步骤 | 装一次即可，不重要 |
| 单元测试 JUnit 注解细节 | 会用 @Test/@BeforeEach 即可 |
| Linux 命令大全 | 多敲就行，不靠背 |
| 文件上传本地存储 | 已淘汰，重点看云存储思路 |

---

## 01 · Web 基础（HTML+CSS）🟢

**概念：** Web 前端三大件：**HTML**（结构）、**CSS**（表现）、**JS**（行为）。Web 标准由 W3C 制定。

**只需掌握的核心标签**（其余 AI 生成）：
- 标题：`<h1>~<h6>`；超链接：`<a href="" target="">`；图片：`<img src="">`
- 布局：`<div>`（块级，独占一行，可设宽高）、`<span>`（行内，多个同行，不可设宽高）
- 表单：`<form action method>` → `input/select/textarea`
  - `GET`：参数拼在 URL 后（`?name=Tom&age=12`），长度有限制
  - `POST`：参数在请求体，长度无限制
  - 表单项必须带 `name` 才能提交

**CSS 引入三种方式**：行内（不常用）、内部（`<style>`，页面级）、**外部（`<link>`，企业常用）**。

**盒子模型（重点）**：content / padding（内边距）/ border（边框）/ margin（外边距）。`box-sizing: border-box` 让 width/height 包含内边距和边框。`padding: 20px 10px` = 上下/左右。

**flex 布局（重点）**：父容器 `display:flex` 控制子元素排列。核心属性 `justify-content`（主轴对齐）：`space-between` 两端对齐平分、`center` 居中、`flex-end` 靠右。横向布局用 `row`，纵向用 `column`。

**版心居中**：`margin: 0 auto;`（配合宽度，如 `width:70%`）。

---

## 02 · JavaScript 🟡

**概念：** JS 是一门跨平台、面向对象的脚本语言，用于控制网页行为。与 Java 语法类似但完全不同。

**变量声明（重点）**：
- `let` 声明变量（弱类型，可存任意类型值）
- `const` 声明常量（不可重新赋值）
- `var` 旧语法，不严谨，**不推荐**

**数据类型**：`typeof` 查看类型。number / string / boolean / null（typeof 是 object）/ undefined。字符串可用单双引号或反引号。

**模板字符串（重点）**：反引号 + `${}` 拼接变量
```js
console.log(`你好,我是${name},今年${age}岁`)
```

**函数两种写法**：
```js
// 命名函数
function add(a,b){ return a+b; }

// 箭头函数（前端开发更常用）
const add = (a,b) => { return a+b; }
```

**JSON 与 JS 对象互转**：
```js
JSON.stringify(obj)   // JS对象 → JSON字符串
JSON.parse(str)       // JSON字符串 → JS对象
```

**DOM 操作（重点）**：
```js
document.querySelector('选择器')      // 获取第一个匹配元素
document.querySelectorAll('选择器')   // 获取所有（伪数组）
element.innerHTML = '内容'            // 改内容
element.style.backgroundColor = 'red' // 改样式
```

**事件监听（重点）**：`事件源.addEventListener('事件类型', 函数)`
- 常用事件：click、mouseenter（移入）、mouseleave（移出）、blur（失焦）、focus（聚焦）、input、keydown
- `addEventListener` 优于 `onclick`（on 会被覆盖）

---

## 03 · Vue + Ajax/Axios 🟡

### 1. Vue 核心

**概念：** Vue 是一款用于**构建用户界面**的**渐进式** JS **框架**。核心思想：**数据驱动视图** —— 数据变了视图自动变，不用手动操作 DOM。

**Vue 三步固定结构**：
```js
import { createApp } from 'vue'
createApp({
  data(){ return { message: 'Hello' } }  // 数据定义在 data 中
}).mount('#app')  // 挂载到 #app 元素
```

**插值表达式**：`{{ message }}` 渲染数据。**必须在 Vue 接管的 `#app` 区域内**。

### 2. Vue 常用指令（重点）

| 指令 | 作用 | 示例 |
|------|------|------|
| `v-for` | 列表渲染 | `<tr v-for="(emp,i) in list" :key="emp.id">` |
| `v-bind` / `:` | 动态绑定属性 | `:src="emp.image"` |
| `v-if` / `v-else-if` / `v-else` | 条件渲染（创建/移除节点） | 频繁切换用 v-show |
| `v-show` | 显示/隐藏（控制 display） | 频繁切换场景用 |
| `v-model` | **双向数据绑定**（表单） | `<input v-model="name">` |
| `v-on` / `@` | 绑定事件 | `@click="search"` |

**v-model 双向绑定**：Vue 数据变 → 视图变；视图输入变 → 数据变。方法定义在 `methods` 中，`this` 指向 Vue 实例。

**面试点：v-if vs v-show**
- v-if：条件成立才渲染节点（创建/移除），适合不频繁切换
- v-show：始终渲染，只是 `display:none` 隐藏，适合频繁切换

### 3. Vue 生命周期（重点）

**mounted**：挂载完成，页面渲染成功。**常用于页面初始化时自动发请求加载数据**。
```js
mounted(){ this.search() }
```

### 4. Ajax / Axios

**Ajax**：异步的 JS 和 XML，用于**不刷新页面**地与服务器交换数据、更新局部网页。

**Axios**（重点，对 Ajax 的封装）：
```js
// GET
axios.get('url').then(res => { console.log(res.data) })
// POST
axios.post('url', data).then(res => { ... })

// async/await 写法（推荐，更清晰）
const result = await axios.get('url')
this.empList = result.data
```

---

## 04 · Maven 🟢

**概念：** Maven 是管理和构建 Java 项目的工具，解决**依赖管理、统一项目结构、标准构建流程**三个问题。

**核心概念**：
- **坐标**：`groupId`（组织）+ `artifactId`（模块）+ `version`（版本），唯一定位依赖
- **依赖**：在 `pom.xml` 的 `<dependencies>` 中声明，Maven 自动下载
- **依赖传递**：A 依赖 B，B 依赖 C，则 A 也有 C（不需要可 `<exclusion>` 排除）
- **仓库**：本地仓库 → 远程仓库（私服/阿里云）→ 中央仓库
- **生命周期**：`clean → compile → test → package → install`（执行后面会自动执行前面同套的）

**常用命令**：`mvn compile`、`mvn test`、`mvn package`（打包 jar）、`mvn install`

**Maven 标准目录**：`src/main/java`（源码）、`src/main/resources`（配置）、`src/test/java`（测试）。

---

## 05 · HTTP 协议 🟡

**概念：** HTTP 是超文本传输协议，规定**浏览器与服务器之间数据传输的规则**。

**三大特点**：
- 基于 TCP（面向连接，可靠）
- 请求-响应模型（先请求后响应，一一对应）
- **无状态**（每次请求独立，不记数据）→ 这就是需要**会话技术（Cookie/Session/令牌）**的原因

### 1. 请求协议

**组成**：请求行 + 请求头 + 请求体

- **请求行**：`请求方式 资源路径 协议/版本`，如 `GET /brand/findAll?name=OPPO&status=1 HTTP/1.1`
- **请求头**：key:value，如 `Host`、`User-Agent`（浏览器版本）、`Content-Type`
- **请求体**：POST 的请求参数在这（GET 参数在 URL，无请求体）

**GET vs POST（面试重点）**：

| | GET | POST |
|---|---|---|
| 参数位置 | 请求行（URL 后） | 请求体 |
| 长度 | 有限制 | 无限制 |
| 安全性 | 低（暴露地址栏） | 较高 |

### 2. 响应协议

**组成**：响应行 + 响应头 + 响应体

- **响应行**：`协议/版本 状态码 描述`，如 `HTTP/1.1 200 OK`
- **响应状态码（必须记）**：`200` 成功 / `404` 资源不存在 / `500` 服务端异常 / `401` 未认证

**响应头**：`Content-Type`（类型）、`Content-Length`（长度）、`Set-Cookie`（设置 Cookie）。

---

## 06 · SpringBoot + 三层架构 + IOC/DI 🔴

### 1. SpringBoot 入门

**概念：** SpringBoot 用于**快速构建** Spring 应用，两大特点：**简化配置、快速开发**。是现在企业开发的主流。

**为什么一个 main 方法就能启动 Web 应用？**
→ 因为引入了 `spring-boot-starter-web` 起步依赖，它通过依赖传递带入了内嵌的 Tomcat。

**入门三步**：
1. 创建 SpringBoot 工程，勾选 Web 依赖
2. 定义 Controller + 注解
3. 运行引导类（带 `@SpringBootApplication`），浏览器访问

```java
@RestController // 请求处理类（= @Controller + @ResponseBody）
public class HelloController {
    @RequestMapping("/hello") // 请求路径
    public String hello(String name){
        return "Hello " + name;
    }
}
```

**@ResponseBody**：把方法返回值直接响应给浏览器；返回值是对象/集合时**自动转成 JSON**。`@RestController` 已包含此注解。

### 2. HTTP 请求/响应获取

**Web 服务器（Tomcat）把请求封装成 `HttpServletRequest`，响应封装成 `HttpServletResponse`**，传给 Controller 方法。

```java
// 获取请求参数
String name = request.getParameter("name");
// 获取请求路径 / 方式 / 头
request.getRequestURI();  request.getMethod();  request.getHeader("User-Agent");
```

### 3. 三层架构（重点）

**为什么分层？** 职责单一、复用性强、便于维护、利于扩展。

| 层 | 包名 | 职责 |
|----|------|------|
| Controller 控制层 | `controller` | 接收请求、响应数据 |
| Service 业务层 | `service` | 处理业务逻辑 |
| Dao/Mapper 数据访问层 | `dao` / `mapper` | 操作数据库（增删改查）|

**请求流程**：前端请求 → Controller → Service → Mapper → 数据库，再一层层返回。

### 4. IOC 控制反转（🔴 Spring 第一大核心）

**概念：** IOC 将**对象的创建控制权从程序本身转移到外部容器**。程序不再 `new` 对象，而是由 **IOC 容器（Spring 容器）** 创建和管理对象。

- **对象声明交给容器**：实现类上加 `@Component`（或其衍生注解）
  - `@Controller`（控制层）、`@Service`（业务层）、`@Repository`（数据访问层）、`@Component`（其他）
- **组件扫描**：`@SpringBootApplication` 默认扫描**启动类所在包及其子包**，所以业务类要放在启动类包下

**解耦的好处**：换实现类不用改 Controller 代码。

### 5. DI 依赖注入（🔴 Spring 第二大核心）

**概念：** 容器为程序**运行时自动提供所依赖的对象**。用 `@Autowired` 按类型自动装配。

**三种注入方式**：

```java
// ① 属性注入（简洁，最常用）
@Autowired
private UserService userService;

// ② 构造器注入（官方推荐，依赖清晰）
private final UserService userService;
@Autowired  // 只有一个构造器时可省略
public UserController(UserService userService){ this.userService = userService; }

// ③ setter 注入
```

**多实现类问题（面试重点）**：容器里有多个同类型 bean 时 `@Autowired` 会报错，三种解决：
- `@Primary`：指定默认实现
- `@Qualifier("bean名")`：指定名称（配合 @Autowired）
- `@Resource(name="bean名")`：按名称注入（JDK 提供）

**面试题：@Autowired vs @Resource**
- @Autowired 是 Spring 提供的，**按类型**注入
- @Resource 是 JDK 提供的，**按名称**注入

---

## 07 · MyBatis + 数据库操作 🔴

### 1. MyBatis 入门

**概念：** MyBatis 是一款优秀的**持久层（DAO）框架**，用于**简化 JDBC 开发**。

**为什么用 MyBatis？** JDBC 缺点：参数硬编码、结果封装繁琐、频繁创建/关闭连接。MyBatis 解决：配置在配置文件、自动映射封装、用**连接池**避免频繁创建连接。

**入门三步**：
1. pom.xml 引入 `mybatis-spring-boot-starter` + mysql 驱动
2. application.yml 配数据源
3. 定义 Mapper 接口 + `@Mapper` 注解 + SQL

```java
@Mapper  // MyBatis 会为接口生成代理对象，交给容器管理
public interface UserMapper {
    @Select("select * from user")
    List<User> findAll();
}
```

**配置数据库连接（application.yml）**：
```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/web
    username: root
    password: 1234
```

### 2. 连接池（重点概念）

**数据库连接池**：容器，负责分配/管理数据库连接。程序启动时创建一定数量连接，使用时借、用完还（复用），避免频繁创建销毁。

常见连接池：**Hikari**（SpringBoot 默认）、**Druid**（阿里，性能好，可切换）。

### 3. MyBatis 增删改查（注解方式）

**#{} vs ${}（面试重点）**：
- `#{}`：预编译占位符（生成 `?`），**防 SQL 注入**，推荐
- `${}`：字符串拼接，有注入风险，不推荐

```java
@Delete("delete from user where id = #{id}")     // 单个参数
@Insert("insert into user(username) values(#{username})")  // 对象参数：#{属性名}
@Update("update user set name=#{name} where id=#{id}")
@Select("select * from user where username=#{username} and password=#{password}")
```

- DML 语句可返回 `int`（影响行数）
- 多个参数：封装成对象，用 `#{对象属性}` 取

**主键返回**（新增后拿 id）：`@Options(useGeneratedKeys=true, keyProperty="id")`

### 4. XML 映射（复杂 SQL 用这个）

**规范**：
1. XML 与 Mapper 接口**同名同包**
2. XML 的 `namespace` = 接口全限定名
3. SQL 的 `id` = 接口方法名

```xml
<mapper namespace="com.itheima.mapper.EmpMapper">
    <select id="list" resultType="com.itheima.pojo.Emp">
        select * from user
    </select>
</mapper>
```

**注解 vs XML**：简单增删改查用注解；复杂 SQL 用 XML。

### 5. 动态 SQL（重点，条件查询必用）

```xml
<select id="list" resultType="com.itheima.pojo.Emp">
    select e.* from emp e
    <where>  <!-- 自动生成 where 并去掉多余 and/or -->
        <if test="name != null and name != ''">
            e.name like concat('%',#{name},'%')
        </if>
        <if test="gender != null">
            and e.gender = #{gender}
        </if>
    </where>
</select>
```

**常用标签**：`<if>`（条件拼接）、`<where>`（自动 where）、`<foreach>`（循环批量）、`<set>`（更新）。

**批量插入（foreach）**：
```xml
<insert id="insertBatch">
    insert into emp_expr (emp_id, company) values
    <foreach collection="list" item="e" separator=",">
        (#{e.empId}, #{e.company})
    </foreach>
</insert>
```

### 6. 分页查询

**PageHelper 分页插件**（重点，避免手写两条 SQL）：
```java
PageHelper.startPage(page, pageSize);  // 设置分页参数
List<Emp> list = empMapper.list();     // 正常查询
Page<Emp> p = (Page<Emp>) list;
return new PageResult(p.getTotal(), p.getResult());  // 封装总条数+数据
```
注意：SQL 结尾不能加分号；PageHelper 只对紧随其后的第一条 SQL 生效。

### 7. 多表关系（设计层面）

| 关系 | 实现方式 |
|------|---------|
| 一对多 | 多的一方加外键字段（如 emp.dept_id）|
| 多对多 | 建**中间表**，含两个外键 |
| 一对一 | 任意一方加外键，且 UNIQUE |

**物理外键 vs 逻辑外键（面试重点）**：企业**几乎不用物理外键**（影响增删改效率、不适合分布式、易死锁），都在业务层用**逻辑外键**。

**多表查询**：
```sql
-- 内连接（交集）
select * from emp e inner join dept d on e.dept_id = d.id;
-- 左外连接（左表全 + 匹配）
select * from emp e left join dept d on e.dept_id = d.id;
-- 子查询（嵌套）
select * from emp where dept_id in (select id from dept where name='教研部');
```

**`resultMap` 处理复杂封装**（一对多查工作经历）：字段对不上、属性复杂时用 `<resultMap>` + `<collection>` 手动映射。

**驼峰映射**：数据库 `create_time` ↔ 实体 `createTime`，配 `map-underscore-to-camel-case: true` 自动转。

---

## 08 · 项目实战（部门/员工管理 + 事务 + 上传）🟡🔴

### 1. 开发规范

**前后端分离**：前端工程、后端工程分开，通过**接口文档**（统一响应格式）协作。

**RESTful 风格（重点）**：通过 URL 定位资源 + HTTP 动词描述操作。
- GET：查询（`/users/1`）
- POST：新增（`/users`）
- PUT：修改（`/users`）
- DELETE：删除（`/users/1`）

**统一响应结果 Result**：
```java
Result.success(data)  // code=1 成功
Result.error(msg)     // code=0 失败
```

**映射注解**：`@GetMapping` / `@PostMapping` / `@PutMapping` / `@DeleteMapping`（都是 @RequestMapping 的衍生）。

### 2. 参数接收（重点）

| 参数类型 | 注解 | 场景 |
|---------|------|------|
| 简单参数（query 串） | 无/@RequestParam | `?id=1` |
| JSON 参数 | `@RequestBody` | 请求体是 JSON，封装成对象 |
| 路径参数 | `@PathVariable` | `/users/{id}` |
| 日期参数 | `@DateTimeFormat(pattern="yyyy-MM-dd")` | 前端传字符串日期 |

```java
@DeleteMapping("/depts/{id}")
public Result delete(@PathVariable Integer id){ ... }

@PostMapping("/depts")
public Result save(@RequestBody Dept dept){ ... }
```

**驼峰映射**：`?name=张&begin=2024-01-01` 这类多个参数，可封装成一个实体类（属性名与参数名一致）接收。

### 3. 事务管理（🔴 必考）

**概念：** 事务是一组操作，**要么全部成功，要么全部失败**。保证数据一致性、完整性。

**Spring 事务**：业务方法上加 `@Transactional`
- **位置**：业务层方法/类/接口上
- **默认只有 RuntimeException（运行时异常）才回滚** → 需要回滚所有异常用 `@Transactional(rollbackFor = Exception.class)`

**事务传播行为（面试重点）**：
- `REQUIRED`（默认）：有事务就加入，没有则新建
- `REQUIRES_NEW`：无论有无都新建独立事务（用于"日志必须记录成功，不影响主事务"的场景）

**四大特性 ACID**：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。

### 4. 文件上传

**表单三要素**：① `file` 域 ② `POST` 方式 ③ `enctype="multipart/form-data"`

**服务端接收**：`MultipartFile` 类型参数
```java
@PostMapping("/upload")
public Result upload(MultipartFile file) {
    // 唯一文件名，避免覆盖
    String ext = file.getOriginalFilename().substring(/* 取后缀 */);
    String newName = UUID.randomUUID() + ext;
    file.transferTo(new File("D:/images/" + newName));
}
```

**云存储（重点思路，阿里云 OSS）**：本地磁盘存储不安全、容量有限、无法直接访问。企业用云存储（OSS），**SDK 使用思路通用**：引入 SDK 依赖 → 参照官方示例 → 配置自己的 endpoint/bucket/凭证 → 调用 API 上传。参数用 `@ConfigurationProperties` 从配置文件读取，便于维护。

### 5. 全局异常处理器（重点）

**问题**：没做异常处理时，框架返回的 JSON 不符合统一格式，前端解析不了。

**解决**：定义全局异常处理器，统一捕获返回 Result。
```java
@RestControllerAdvice  // = @ControllerAdvice + @ResponseBody
public class GlobalExceptionHandler {
    @ExceptionHandler
    public Result ex(Exception e){
        e.printStackTrace();
        return Result.error("操作失败,请联系管理员");
    }
}
```

---

## 09 · 登录认证（JWT + Filter/Interceptor）🔴

### 1. 会话跟踪技术（面试重点）

HTTP 无状态，需要会话技术记住登录状态。三种方案对比：

| 方案 | 存储位置 | 优点 | 缺点 |
|------|---------|------|------|
| **Cookie** | 客户端浏览器 | 协议自动携带 | 移动端不支持、不安全、不能跨域 |
| **Session** | 服务端 | 安全 | 集群环境无法使用、底层依赖 Cookie |
| **令牌（JWT）** | 客户端（前端存储）| 支持移动端、解决集群、减轻服务端压力 | 需自己实现 |

**企业主流用 JWT 令牌技术**。

### 2. JWT 令牌

**概念：** JWT（JSON Web Token）是简洁的、自包含的、安全的令牌，本质是一个**带签名防篡改的字符串**。

**三部分**（用 `.` 分隔）：Header（头，算法） + Payload（负载，自定义数据如 id/username） + Signature（签名，防篡改）。

**JWT 特点**：篡改任何一个字符，校验都会失败；过期后失效。Base64 是编码不是加密。

```java
// 生成
String jwt = Jwts.builder().signWith(SignatureAlgorithm.HS256, "密钥")
    .addClaims(claims)                    // 自定义数据
    .setExpiration(new Date(...))         // 过期时间
    .compact();

// 校验
Claims claims = Jwts.parser().setSigningKey("密钥")
    .parseClaimsJws(jwt).getBody();
```

**登录流程**：登录成功 → 生成 JWT → 返回前端 → 前端存 localStorage → 后续每次请求带在请求头 `token` → 服务端统一拦截校验。

### 3. 登录校验：Filter 过滤器

**Filter**：JavaWeb 三大组件之一，拦截请求做通用操作（登录校验、统一编码等）。

**两个注解**：类上加 `@WebFilter(urlPatterns="/*")`，启动类加 `@ServletComponentScan`。

**核心方法**：
```java
public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain){
    // 1. 获取 url
    // 2. 若是 login 请求 → 放行
    // 3. 获取请求头 token
    // 4. 无 token → 返回 401
    // 5. 解析 token 失败 → 返回 401
    // 6. 校验通过 → chain.doFilter() 放行
}
```

### 4. 登录校验：Interceptor 拦截器

**Interceptor**：Spring 提供的，拦截**控制器方法**。实现 `HandlerInterceptor`，`preHandle` 返回 true 放行/false 拦截。

**两步**：定义拦截器（`@Component`）→ 配置注册（`WebConfig` 实现 `WebMvcConfigurer`）。
```java
registry.addInterceptor(tokenInterceptor)
    .addPathPatterns("/**")                // 拦截所有
    .excludePathPatterns("/login");        // 排除登录
```

**Filter vs Interceptor（面试重点）**：
- 接口规范不同：Filter 实现 Filter 接口；Interceptor 实现 HandlerInterceptor 接口
- **拦截范围不同：Filter 拦截所有资源；Interceptor 只拦截 Spring 环境（Controller）中的资源**

---

## 10 · AOP 面向切面 🔴

### 1. 概念

**概念：** AOP（Aspect Oriented Programming）面向切面编程 = **面向特定方法编程**。在**不改动原始业务代码**的前提下，给方法增加共性功能（如日志、耗时统计、事务）。

**优势**：减少重复代码、代码无侵入、提高开发效率、维护方便。

### 2. 核心概念

| 概念 | 含义 |
|------|------|
| **连接点 JoinPoint** | 可以被 AOP 控制的方法 |
| **通知 Advice** | 重复的共性逻辑（最终是一个方法）|
| **切入点 PointCut** | 匹配连接点的条件（哪些方法应用通知）|
| **切面 Aspect** | 通知 + 切入点 = 切面 |
| **目标对象 Target** | 通知应用的对象 |

**底层原理**：基于**动态代理**，运行时为目标对象生成代理对象进行功能增强。

### 3. 入门（重点，统计耗时）

```java
@Aspect   // 切面类
@Component
public class TimeAspect {
    @Around("execution(* com.itheima.service.impl.DeptServiceImpl.*(..))")
    public Object recordTime(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object result = pjp.proceed();  // 执行原始方法
        long end = System.currentTimeMillis();
        log.info("耗时:{}ms", end - begin);
        return result;
    }
}
```

依赖：`spring-boot-starter-aop`。

### 4. 通知类型（重点）

| 注解 | 执行时机 |
|------|---------|
| `@Around` | 目标方法前后都执行（最强大，需自己调 proceed）|
| `@Before` | 目标方法前 |
| `@After` | 目标方法后（无论有无异常都执行）|
| `@AfterReturning` | 正常返回后 |
| `@AfterThrowing` | 异常后 |

**@Around 注意**：返回值必须是 `Object`；原始方法异常后，环绕后置代码不执行。

### 5. 切入点表达式

**execution（按方法签名匹配）**：
```
execution(访问修饰符? 返回值 包名.类名.方法名(参数) throws异常?)
```
- `*`：通配任意（一个）
- `..`：任意层级包 / 任意个数参数
- 例：`execution(* com.itheima.service.*.*(..))`

**@annotation（按自定义注解匹配）**：方法名不规则时用。自定义注解 → 在目标方法加注解 → 切入点写 `@annotation(com.xxx.LogOperation)`。

**抽取公共切入点**：`@Pointcut("...")` 定义，`@Before("pt()")` 引用。

### 6. 操作日志案例 + ThreadLocal

**ThreadLocal（重点）**：线程局部变量，每个线程有独立存储，**线程隔离**。用于在同一线程内（如登录用户从 Filter 传到 Service/AOP）共享数据。
```java
CurrentHolder.setCurrentId(id);   // set
CurrentHolder.getCurrentId();     // get
CurrentHolder.remove();           // 用后清除，防内存泄漏
```

---

## 11 · 部署（Linux + Docker）🟢

### 1. Linux 常见命令

**只需记住常用的**（多敲即可）：
- 目录：`ls`、`cd`、`mkdir -p`、`rm -rf`
- 文件：`cat`、`tail -f`（看日志）、`head`、`vi/vim`（编辑）
- 拷贝移动：`cp -r`、`mv`
- 打包：`tar -zxvf xx.tar.gz`（解压）、`tar -zcvf`（压缩）
- 查找：`find`、`grep`
- 软件：`yum install`、`rpm`

### 2. 后端部署（Linux）

**打包** → 上传 jar 到 `/usr/local/app` → 后台运行：
```bash
# 打包（跳过测试）
mvn package -DskipTests
# 后台运行，日志输出到文件
nohup java -jar tlias.jar &> tlias.log &
# 停止
ps -ef | grep tlias
kill -9 进程号
```

### 3. Docker（重点概念）

**概念**：Docker 用**镜像**（image）封装应用+运行环境，运行出**容器**（container），实现跨系统快速部署。

**核心命令**：
```bash
docker run -d --name mysql -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123 mysql:8
docker images        # 看镜像
docker ps            # 看容器
docker exec -it nginx bash   # 进入容器
docker logs -f 容器名        # 看日志
```

**数据卷（volume）**：容器内目录 ↔ 宿主机目录映射，让数据/配置与容器解耦（升级不丢数据）。

**自定义镜像 Dockerfile**：`FROM`（基础镜像）→ `COPY`（拷贝）→ `ENV`（环境变量）→ `RUN`（命令）→ `ENTRYPOINT`（启动命令）。
```bash
docker build -t 镜像名 .
docker run -d --name 容器名 -p 8080:8080 镜像名
```

**DockerCompose**：用一个 `docker-compose.yml` 定义多个关联容器（MySQL + 后端 + Nginx），一键部署 `docker compose up -d`。

---

## 12 · 项目实战对照（真实代码：泰乐员工管理系统）🔴

> 依据真实项目 `F:/java/web/webproject01/webaipro2/taile` 逐层拆解，把前面学到的理论对应到真实代码。
> 技术栈：Spring Boot + MyBatis + MySQL(db06) + 阿里云 OSS + 原生 JS(fetch) 前端。

### 1. 项目结构 = 三层架构落地

```
taile/
├─ pom.xml                     # Maven 坐标 + 依赖
├─ src/main/java/org/
│  ├─ taile/                   # 启动类 + 配置(CorsConfig)
│  ├─ controller/              # 控制层 4 个
│  ├─ service/ + impl/         # 业务层接口 + 实现
│  ├─ mapper/                  # 数据访问层(接口)
│  ├─ pojo/                    # 实体 + 参数 + 响应封装
│  └─ utils/                   # OSS 工具 + OSS 配置类
└─ src/main/resources/
   ├─ application.yml          # 数据源/MyBatis/OSS 配置
   ├─ mapper/*.xml             # 复杂 SQL
   └─ static/index.html        # 前端页面
```

**启动类（关键）**：黑马讲义默认 `@SpringBootApplication` 只扫描**启动类所在包及其子包**。本项目启动类在 `org.taile`，而 Controller/Service/Mapper 在 `org.controller` 等平级包，**不在子包里，所以必须显式声明扫描范围**：

```java
@SpringBootApplication
@MapperScan("org.mapper")                        // 扫描所有 Mapper 接口
@ComponentScan(basePackages = {"org.taile","org.controller","org.service","org.mapper","org.utils"})
public class TaileApplication {
    public static void main(String[] args) { SpringApplication.run(...); }
}
```

### 2. 统一响应 Result（前后端分离的接口契约）

真实代码 `Result.java`：`code` 1成功/0失败，`msg` 消息，`data` 数据。

```java
Result.success()        // code=1 msg=操作成功
Result.success(data)    // code=1 带数据
Result.error(msg)       // code=0
```
前端 `if(json.code !== 1)` 判断是否成功 —— 这就是**接口文档统一响应格式**的落地。

### 3. 参数接收三件套（真实对照笔记 08）

| 场景 | 真实代码 | 说明 |
|------|---------|------|
| query 串封装实体 | `list(empQueryPara empQueryPara)` | 多个查询参数封进一个实体，**默认值在实体里**：`page=1, pageSize=5` |
| 日期参数 | 实体字段 `@DateTimeFormat(pattern="yyyy-MM-dd") LocalDate start` | 注解**写在实体字段上**而非方法参数上 |
| JSON 请求体 | `@RequestBody Dept dept` | POST/PUT 新增修改 |
| 路径参数 | `@PathVariable Integer id` `GET /emps/{id}` | 查详情 |
| 批量删除 | `@RequestParam List<Integer> ids` | 前端传 `?ids=1,2,3` 逗号串 |

**实体封装参数的好处**：前端 `?name=张&gender=男&start=..&end=..` 全自动映射进 `empQueryPara`，方法签名干净。

### 4. RESTful 接口清单（真实完整）

| 模块 | 方法+路径 | 功能 | 参数 |
|------|----------|------|------|
| 部门 | `GET /depts` | 列表 | — |
| | `DELETE /depts` | 删除 | `id` |
| | `POST /depts` | 新增 | JSON |
| | `GET /depts/{id}` | 详情 | 路径 |
| | `PUT /depts` | 修改 | JSON |
| 员工 | `GET /emps` | 分页+条件查询 | `page/pageSize/name/gender/start/end` |
| | `POST /emps` | 新增(含工作经历) | JSON |
| | `DELETE /emps` | 批量删除 | `ids` 数组 |
| | `GET /emps/{id}` | 详情(含工作经历) | 路径 |
| | `PUT /emps` | 修改(重插工作经历) | JSON |
| 上传 | `POST /upload` | OSS 上传 | multipart |
| 报表 | `GET /report/empJobData` | 岗位分布统计 | — |
| | `GET /report/empGenderData` | 性别统计 | — |

### 5. MyBatis：注解 vs XML 的真实分工

- **deptMapper 全用注解**（简单增删改查）：`@Select/@Delete/@Insert/@Update`
- **empMapper / empWorkMapper 用 XML**（复杂 SQL：条件查询、动态更新、批量、一对多、报表聚合）

→ 印证笔记 07：**简单 CRUD 用注解，复杂 SQL 用 XML**。

注解方式（deptMapper，含主键返回场景）：
```java
@Select("select * from dept")
List<Dept> findAll();

@Options(useGeneratedKeys = true, keyProperty = "id")  // 新增后自动回填 id
@Insert("INSERT INTO employee_info(username,name,gender,...) VALUES(#{userName},#{name},...)")
void insertExper(emp emp);
```

### 6. 动态 SQL 真实四例

**① 条件分页查询 list**（`<where>` 自动去多余 and + `<if>`）：
```xml
<select id="list" resultType="org.pojo.emp">
    select e.*, d.name as dept_name
    from employee_info e left join dept d on e.dept_id = d.id
    <where>
        <if test="name != null and name != ''"> e.name like concat('%',#{name},'%') </if>
        <if test="gender != null"> and e.gender = #{gender} </if>
        <if test="start != null and end != null"> and e.entry_date between #{start} and #{end} </if>
    </where>
    order by e.create_time desc
</select>
```
> 用 `concat('%',#{name},'%')` 而非 `${}`，**防 SQL 注入**（对应笔记 07 重点）。

**② 动态更新 updateEmp**（`<set>` 自动去末尾逗号 + 每个字段 `<if>` 判断是否要更新）：
```xml
<update id="updateEmp">
    UPDATE employee_info
    <set>
        <if test="name != null and name != ''"> name = #{name}, </if>
        <if test="gender != null"> gender = #{gender}, </if>
        ...
        <if test="updateTime != null"> update_time = #{updateTime} </if>
    </set>
    WHERE id = #{id}
</update>
```

**③ 批量删除 deleteEmp**（`<foreach>` + `in`）：
```xml
<delete id="deleteEmp">
    delete from employee_info where id in
    <foreach collection="ids" item="id" open="(" close=")" separator=",">
        #{id}
    </foreach>
</delete>
```

**④ 批量插入工作经历**（`<foreach>` 多值）：
```xml
<insert id="insert">
    insert into work_experience(emp_id,begin_date,end_date,company,job) values
    <foreach collection="list" item="expr" separator=",">
        (#{expr.empId},#{expr.beginDate},#{expr.endDate},#{expr.company},#{expr.job})
    </foreach>
</insert>
```

### 7. resultMap + collection（一对多真实例子）

员工表 `employee_info` 与工作经历表 `work_experience` 是 **1 对多**。查员工详情要同时带出他的多条工作经历 → 用 `resultMap` + `<collection>` 手动映射（对应笔记 07 的"复杂封装"）。

```xml
<resultMap id="empResultMap" type="org.pojo.emp">
    <result column="id" property="id"/>
    ...
    <result column="dept_name" property="deptName"/>
    <collection property="exprList" ofType="org.pojo.empWork">  <!-- 一对多：list 属性 -->
        <result column="emp_id" property="empId"/>
        <result column="company" property="company"/>
        ...
    </collection>
</resultMap>

<select id="selectEmp" resultMap="empResultMap">
    select e.*, w.* from employee_info e
    left join work_experience w on e.id = w.emp_id
    where e.id = #{id}
</select>
```
> 为什么这里不用"驼峰自动映射"？因为涉及子集合对象，**必须显式 resultMap** 才能把关联子表映射进 `exprList`。单表简单查询才靠 `map-underscore-to-camel-case: true`。

**实体里的 List 属性**：
```java
@Data
public class emp {
    private Integer id; ...
    private List<empWork> exprList;   // 一个员工对应多条工作经历
}
```

### 8. 分页 PageHelper 真实写法

```java
@Override
public pageResult<emp> list(empQueryPara para) {
    PageHelper.startPage(para.getPage(), para.getPageSize()); // ① 先设分页
    List<emp> list = empMapper.list(para);                    // ② 正常查询
    Page<emp> p = (Page<emp>) list;                           // ③ 强转 Page
    return new pageResult<>(p.getTotal(), p.getResult());     // ④ 封装 total + rows
}
```
- `pageResult<T>{ long total; List<T> rows; }` 是通用分页响应体
- 注意：**PageHelper 只对紧随其后第一条 SQL 生效**；SQL 结尾不能带分号。

### 9. 事务 @Transactional 真实两处

```java
@Transactional   // 默认：只回滚 RuntimeException
public void insertExper(emp emp){
    empMapper.insertExper(emp);              // 先插员工
    List<empWork> exprList = emp.getExprList();
    if(!CollectionUtils.isEmpty(exprList)){
        exprList.forEach(w -> w.setEmpId(emp.getId()));  // 用回填的主键
        empWorkMapper.insert(exprList);      // 再插多条工作经历
    }
}

@Transactional(rollbackFor = Exception.class)  // 回滚所有异常
public void deleteEmp(List<Integer> ids){
    empMapper.deleteEmp(ids);        // 删员工
    empWorkMapper.deleteEmp(ids);    // 删其工作经历（两边都删才一致）
}
```
> 业务场景 = 笔记 08 的"主从一起成功/一起失败"。员工与其工作经历**同生共死**，必须同一事务。

### 10. 报表聚合 SQL（case when / if）

报表接口返回统计图数据（岗位分布、性别分布），用**聚合 SQL** 在数据库算好，前端直接渲染：

```xml
<!-- 岗位分布：按 dept_id 分类计数 -->
<select id="countEmpJobData" resultType="java.util.Map">
    select (case dept_id
        when 1 then '班主任' when 2 then '讲师'
        when 3 then '学工主管' when 4 then '咨询师'
        else '其他' end) as pos, count(*) as num
    from employee_info group by dept_id
</select>

<!-- 性别分布：if 函数改名计数 -->
<select id="getEmpGenderData" resultType="java.util.Map">
    select if(gender='男','男性员工','女性员工') as name, count(*) as value
    from employee_info group by gender
</select>
```
- 返回类型用 `Map`（SQL 别名即 key），避免为此建实体
- Service 层再把 Map 拆成两条 List 供图表用（`jobList` / `dataList`）

### 11. 阿里云 OSS 上传（云存储真实完整）

**配置读取用 `@ConfigurationProperties`**（对应笔记 08 提到的企业用法）：
```java
@Data @Component
@ConfigurationProperties(prefix = "aliyun.oss")   // 自动绑定 yml 里 aliyun.oss.*
public class utils { private String endPoint; private String bucketName; private String region; }
```

**上传核心 `AliyunOSSOperator.upload()`**：
- 凭证从**环境变量**读取（`OSS_ACCESS_KEY_ID / OSS_ACCESS_KEY_SECRET`），不写死在代码里（安全）
- 文件路径按**日期分目录** `yyyy/MM` + **UUID 重命名**（避免重名覆盖）
- 用 V4 签名（`SignVersion.V4`）
- 返回完整可访问 URL

```java
String dir = LocalDate.now().format(DateTimeFormatter.ofPattern("yyyy/MM"));
String newFileName = UUID.randomUUID() + originalFilename.substring(originalFilename.lastIndexOf("."));
String objectName = dir + "/" + newFileName;
ossClient.putObject(bucketName, objectName, new ByteArrayInputStream(content));
return endPoint.split("//")[0] + "//" + bucketName + "." + endPoint.split("//")[1] + "/" + objectName;
```

**Controller 收文件**：`MultipartFile file` + `file.getBytes()` 传给 OSS（不再写本地磁盘）。
> 前端用 `FormData` + `fetch` 以 multipart 方式 POST 到 `/upload`，拿到 URL 存进 `image` 字段。

### 12. 跨域 CORS（前后端分离必配）

前端 `localhost:8080` 起、页面可能从别的端口访问后端，浏览器会跨域拦截 → 后端开 CORS：
```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOriginPatterns("*")
                .allowedMethods("GET","POST","PUT","DELETE","OPTIONS")
                .allowedHeaders("*")
                .maxAge(3600);
    }
}
```

### 13. 日志

- Controller 用 `@Slf4j`（Lombok 生成 log 对象）或 `LoggerFactory.getLogger(xxx.class)`
- 增删改查都打 `log.info(...)` 方便排查
- yml 里配 `mybatis.configuration.log-impl: Slf4jImpl` 可打印 SQL

### 14. 前端（本项目用原生 fetch，企业多用 Vue）

- 本项目前端 `index.html` 是**原生 JS + fetch**（非 Vue），演示了：分页、条件筛选、勾选批量删除、弹窗表单、头像上传预览
- 前端请求统一：`fetch(url)` → `res.json()` → 判断 `json.code !== 1`
- 上传：`new FormData()` + `fd.append('file', file)` + `POST /upload`
- 批量删除：勾选 checkbox → 收集 id → `DELETE /emps?ids=1,2,3`
- 新增/修改共用一个弹窗：`editingEmp` 有值走 PUT，否则 POST
- 删除工作经历交互注意：前端 `delete body.exprList` 避免误删重插（后端按"整表删了重插"策略）

> 面试可讲：**企业级会把这套前端换成 Vue3 + ElementPlus + Axios**，但接口契约（Result 格式）不变，后端改动很小——这就是前后端分离的价值。

---

## 13 · 泰乐项目代码+注释逐行详解（基于 taile_new）🔴

> 本章是第 12 章的"加深版"：把真实项目 `D:/java/web/webaipro2/taile_new` 的**核心代码逐层贴出来，并对每一处注解 / 代码 / 注释讲清楚"什么时候用、为什么用、有什么用"**。
> 学习方式：先看 13.9 把一条请求串起来，再逐模块对照。面试时能背出这些"为什么"，比光会贴代码强。

### 13.1 启动类与全局配置

#### ① 启动类 Application.java —— 项目一切的入口

```java
@ServletComponentScan          // 【注解1】
@SpringBootApplication         // 【注解2】
@MapperScan("com.taile.mapper")// 【注解3】
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args); // 【代码4】启动 Spring 应用
    }
}
```

- **【注解1】@ServletComponentScan**：让 Spring Boot 扫描 `@WebFilter / @WebServlet / @WebListener` 三类原生 Servlet 组件。本项目里的 `tokenFilter`（Filter 方案，后来被拦截器取代而注释掉）就是这类组件，加上它才能被识别。
- **【注解2】@SpringBootApplication**：组合注解，等价于 `@SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan` 三个拼在一起。`@ComponentScan` 默认**只扫描启动类所在包及其子包**。
- **【注解3】@MapperScan("com.taile.mapper")**：把 `com.taile.mapper` 包下所有 Mapper 接口扫描成 MyBatis 代理对象放进容器。等价于在每个接口上写 `@Mapper`（本项目两种都用，其实留一种即可）。
- **【代码4】SpringApplication.run**：真正把 Spring 应用跑起来——创建 IOC 容器、加载所有 bean、启动内嵌 Tomcat。

> 🔑 **关键对比（和笔记 12 老版）**：老版 Controller 散落在平级包 `org.controller` 等，不在启动类子包下，所以必须显式 `@ComponentScan(basePackages={...})`。`taile_new` 把所有类统一放 `com.taile` 下，靠默认扫描就够。**这印证了笔记 06 的规则：分层包一定要放在启动类所在包及其子包下**。

#### ② application.yml —— 所有配置的"集中营"

```yaml
spring:
  datasource:                                   # MyBatis 连数据库
    url: jdbc:mysql://localhost:3306/db06
    username: root
    password: srq7758258
mybatis:
  mapper-locations: classpath:mapper/*.xml      # 【关键1】告诉 MyBatis 去哪找 XML 映射文件
  configuration:
    map-underscore-to-camel-case: true          # 【关键2】下划线→驼峰自动映射
aliyun:
  oss:
    endPoint: https://oss-cn-beijing.aliyuncs.com
    bucketName: srqxx-web
    region: cn-beijing
```

- **【关键1】mapper-locations**：不配这句，MyBatis 找不到 `mapper` 目录下的 `empMapper.xml` 等文件，所有复杂 SQL（动态查询、resultMap、foreach）全部失效，只留注解 SQL 能用。**写了这句才能"注解 + XML"混用**。
- **【关键2】map-underscore-to-camel-case**：把数据库的 `create_time` 自动转成 Java 属性的 `createTime`。**没有它，`selectEmp` 返回的 `createTime` 会是 null**。但注意：一旦某条 SQL 用了 `resultMap` 显式映射，这个开关就不再管那条 SQL（手动映射优先）。

#### ③ webConfig.java —— 拦截器注册（登录校验的"开关"）

```java
@Configuration                            // 【注解】标记这是配置类，交给容器
public class webConfig implements WebMvcConfigurer {
    @Autowired
    private tokenInterceptor tokenInterceptor;      // 注入拦截器（它是 @Component）

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(tokenInterceptor)   // ① 注册拦截器
                .addPathPatterns("/**")             // ② 拦截所有请求
                .excludePathPatterns(               // ③ 放行以下路径
                        "/", "/login",
                        "/**/*.html", "/images/**",
                        "/favicon.ico", "/error");
    }
}
```

- **为什么实现 WebMvcConfigurer**：这是 Spring MVC 提供的"定制接口"，重写 `addInterceptors` 就能往框架里挂拦截器，完全不用改框架源码。
- **为什么必须 excludePathPatterns**：拦截的是所有请求（`/**`）。如果不放行登录页和静态资源，用户还没登录连登录页、图片都打不开，形成"死锁"。所以登录接口、html、图片、错误页都要排外。

#### ④ CorsConfig.java —— 前后端分离必配的跨域

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")                  // 对后端所有接口开跨域
                .allowedOriginPatterns("*")         // 允许所有来源（前端页面所在地址）
                .allowedMethods("GET","POST","PUT","DELETE","OPTIONS")
                .allowedHeaders("*")
                .maxAge(3600);                      // 预检请求结果缓存3600秒，减少重复预检
    }
}
```

- **为什么需要**：前端页面和后端接口不同端口 → 浏览器"同源策略"拦截 → 后端必须主动声明允许跨域。
- **为什么放行 OPTIONS**：跨域前浏览器会先发一个 OPTIONS"预检请求"，它必须被允许，否则真正请求发不出去。
- **maxAge(3600)**：预检结果缓存 1 小时，减少每次请求都先预检的开销。

---

### 13.2 统一响应与实体（前后端"接口契约"）

#### ① Result.java —— 所有接口统一返回格式

```java
@Data
public class Result {
    private Integer code;   // 1=成功 0=失败：前端 if(json.code !== 1) 判断
    private String msg;     // 提示消息
    private Object data;    // 返回的数据，Object 表示什么类型都能装

    public static Result success(){ ... code=1 msg="操作成功" ... }
    public static Result success(Object data){ ... }
    public static Result error(String msg){ code=0 ... }
}
```

- **为什么用 static 工厂方法而不是 new**：`Result.success(list)` 比 `new Result(); r.setCode(1); r.setData(list);` 简洁得多，而且语义一眼看懂（"这是成功的结果"）。
- **为什么 data 用 Object**：员工接口返回 `pageResult`、部门返回 `List<Dept>`、报表返回 `jobOption`，类型各不同。用 `Object` 统一接收，序列化成 JSON 时按实际类型转。
- **这就是笔记 08 的"统一响应结果"落地**：前端所有地方都用同一套判断逻辑，接口协作不需要各写各的。

#### ② 实体类为什么都用 @Data（Lombok）

```java
@Data   // 自动生成 getter/setter/toString/equals/hashCode
public class emp {
    private Integer id;
    private String userName;
    private List<empWork> exprList;  // 一对多：一个员工多条工作经历
}
```

- **@Data 的作用**：编译时 Lombok 自动补全几十行 getter/setter，省去手写。这也是 pom.xml 里要引入 lombok 依赖 + maven-compiler 配置 `annotationProcessorPaths` 的原因。
- **为什么实体类不加 @Component/@Service**：它们只是"数据载体"。MyBatis 靠反射给属性赋值、Jackson 靠它反序列化请求，都是在需要时由框架自己 `new`，**不交给 IOC 容器管理**，所以不需要标注交给容器的注解。

#### ③ empQueryPara.java —— 查询参数封装（含默认值 + 日期格式）

```java
@Data
public class empQueryPara {
    private Integer page = 1;      // 【重点1】默认第1页
    private Integer pageSize = 5;  // 【重点1】默认每页5条
    private String name;
    private String gender;
    @DateTimeFormat(pattern = "yyyy-MM-dd")  // 【重点2】前端字符串日期 → LocalDate
    private LocalDate start;
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private LocalDate end;
}
```

- **【重点1】默认值写在字段初始值**：前端不传分页参数时也能正常跑（第 1 页每页 5 条），不用后端判空兜底。
- **【重点2】@DateTimeFormat 必须写在字段上**（不同于方法参数注解）。它告诉 Spring 把前端传来的字符串 `"2024-01-01"` 解析成 `LocalDate`。**不写会报"类型转换失败"**。
- **为什么把参数封进实体**：前端 `?name=张&gender=男&start=..&end=..` 全自动映射进一个对象，方法签名干净，不用写一长串形参。

---

### 13.3 Controller 层（控制层）

#### ① loginController.java —— 登录接口

```java
@Slf4j                       // 【注解1】生成 log 日志对象
@RestController               // 【注解2】= @Controller + @ResponseBody
@RequestMapping("/login")     // 【注解3】类级路径前缀
public class loginController {
    @Autowired
    private empService empService;   // 【注解4】注入 Service，不 new

    @PostMapping               // 【注解5】POST /login
    public Result login(@RequestBody emp emp) {   // 【注解6】请求体JSON→emp对象
        log.info("emp:{}", emp);                  // 【代码】打日志
        loginInfo loginInfo = empService.selectLogin(emp);
        if (loginInfo == null) {
            return Result.error("用户名或密码错误");   // 查不到→统一失败格式
        }
        return Result.success(loginInfo);             // 查得到→带token返回
    }
}
```

- **【注解1】@Slf4j**：Lombok 编译期生成 `private static final Logger log`。用 `log.info("emp:{}", emp)`，`{}` 是占位符，**比字符串拼接 `"emp:"+emp` 性能好**（参数只在真正要打日志时才 toString）。
- **【注解2】@RestController**：等于 `@Controller + @ResponseBody`。返回值是对象时自动转 JSON 响应给浏览器。这就是"前后端分离，后端只回 JSON"的关键。
- **【注解4】@Autowired 注入而不 new**：这正是 IOC/DI（笔记 06）。`empService` 由容器创建好，程序只管用。
- **【注解6】@RequestBody**：**必须有**——前端登录传的是 JSON 请求体，没有它 `emp` 收不到任何数据（收的是 null）。
- **【代码】log.info 的作用**：排查问题时能看"收到了什么参数"。**打日志是后端调试的最基本手段**，比 print 更规范（有级别、可控制开关）。

#### ② empController.java —— 员工 CRUD + 自定义注解 @logOperation

```java
@Slf4j
@RestController
@RequestMapping("/emps")
public class empController {
    @Autowired
    private empService empService;

    @logOperation      // 【重点】标记：这个方法要被 AOP 记录操作日志
    @GetMapping        // 分页+条件查询（query 参数自动封装进 empQueryPara）
    public Result list(empQueryPara empQueryPara) { ... }

    @logOperation
    @PostMapping       // 新增（含工作经历）
    public Result insertExper(@RequestBody emp emp) { ... }

    @logOperation
    @DeleteMapping
    public Result deleteEmp(@RequestParam List<Integer> ids) { ... }  // ?ids=1,2,3 逗号串→List

    @logOperation
    @GetMapping("/{id}")
    public Result InsertEmp(@PathVariable Integer id) { ... }          // /emps/3 里的 3

    @logOperation
    @PutMapping
    public Result updateEmp(@RequestBody emp emp) { ... }
}
```

- **@logOperation**：自定义的"标记注解"，本身什么都不干，作用是给切面 `operationLogAspect` 当**匹配信号**（详见 13.7）。想要哪个方法被记日志，就给它加这个注解——这就是 AOP 无侵入。
- **@RequestParam List\<Integer\> ids**：Spring 自动把 `?ids=1,2,3` 的逗号串拆成 List。
- **@PathVariable**：从路径 `/emps/3` 里取 3 赋给 id。
- 五个方法对应五种 HTTP 动词，就是笔记 08 的 **RESTful 风格**落地。

---

### 13.4 Service 层（业务层）—— 事务 / 分页 / 登录

#### empServiceImpl.java（本项目业务最核心）

```java
@Service                       // 【注解】交给容器：业务层组件
public class empServiceImpl implements empService {
    @Autowired
    private empMapper empMapper;          // 注入 Mapper 接口（容器给代理对象）
    @Autowired
    private empWorkMapper empWorkMapper;

    // ---------- ① 分页查询 ----------
    @Override
    public pageResult<emp> list(empQueryPara para) {
        PageHelper.startPage(para.getPage(), para.getPageSize()); // 【1】先设分页参数
        List<emp> list = empMapper.list(para);                     // 【2】正常查询
        Page<emp> p = (Page<emp>) list;                            // 【3】强转 Page
        return new pageResult<>(p.getTotal(), p.getResult());      // 【4】取总条数+数据
    }
    // PageHelper 原理：自动在 SQL 后面拼 LIMIT，并额外执行一条 count 查总数。
    // 注意两个坑：只对紧随其后的第一条 SQL 生效；SQL 结尾不能加分号。

    // ---------- ② 新增员工 + 工作经历（同一事务） ----------
    @Transactional                       // 【重点1】事务：主表子表同生共死
    @Override
    public void insertExper(emp emp) {
        emp.setCreateTime(LocalDateTime.now());
        emp.setUpdateTime(LocalDateTime.now());
        empMapper.insertExper(emp);                          // 先插员工
        List<empWork> exprList = emp.getExprList();
        if (!CollectionUtils.isEmpty(exprList)) {            // 【重点2】空集合跳过
            exprList.forEach(w -> w.setEmpId(emp.getId()));  // 【重点3】用回填主键
            empWorkMapper.insert(exprList);                  // 批量插工作经历
        }
    }
    // 为什么 forEach 里 setEmpId(emp.getId())？因为 empMapper.insertExper 加了
    //   @Options(useGeneratedKeys=true, keyProperty="id")，插完数据库自增主键回填到 emp.id，
    //   工作经历才能挂到正确员工下。没有主键回填，这里 emp.getId() 是 null。

    // ---------- ③ 批量删除（回滚所有异常） ----------
    @Override
    @Transactional(rollbackFor = Exception.class)   // 【重点】默认只回滚RuntimeException
    public void deleteEmp(List<Integer> ids) {
        empMapper.deleteEmp(ids);      // 删员工
        empWorkMapper.deleteEmp(ids);  // 删其工作经历：主表从表一起删才数据一致
    }

    // ---------- ④ 登录：校验成功发 JWT ----------
    @Override
    public loginInfo selectLogin(emp emp) {
        loginInfo loginInfo = empMapper.loginNameAndPassword(emp); // 查用户名+密码
        if (loginInfo == null) return null;                        // 查不到→null
        Map<String, Object> claim = new HashMap<>();
        claim.put("id", loginInfo.getId());        // 放用户 id
        claim.put("username", emp.getUserName());  // 放用户名
        String token = JWTutils.generateJWT(claim);// 生成令牌
        loginInfo.setToken(token);                 // 令牌塞进返回体
        return loginInfo;
    }
}
```

- **【重点1】@Transactional 为什么加在这**：新增员工 + 多条工作经历是"从操作"，必须全部成功或全部回滚，否则可能"员工加上了但经历没加上"，数据不一致。**业务层的这个注解就是笔记 08 事务的落地**。
- **【重点2】CollectionUtils.isEmpty**：空集合校验，避免空指针，也避免传空 list 触发无效 SQL。
- **【重点】@Transactional(rollbackFor = Exception.class)**：默认事务**只对运行时异常 RuntimeException 回滚**，编译期异常（如 SQLException）不会回滚。这里显式要求"所有异常都回滚"，更安全。
- 登录不发事务：只查不写，不需要事务。

---

### 13.5 Mapper 层（持久层）—— 注解 vs XML 的真实分工

#### ① deptMapper.java —— 全注解（简单 SQL 用注解）

```java
@Mapper          // 【注解】MyBatis 为接口生成代理对象放进容器
public interface deptMapper {
    @Select("select * from dept")
    List<Dept> findAll();

    @Delete("delete from dept where id = #{id}")   // #{} 预编译占位符，防SQL注入
    void delete(Integer id);

    @Insert("insert into dept(name,create_time,update_time) values(#{name},#{createTime},#{updateTime})")
    void add(Dept dept);

    @Update("update dept set name = #{name},update_time = #{updateTime} where id = #{id}")
    void update(Dept dept);
}
```

- 全注解、无 XML → **印证笔记 07"简单增删改查用注解"**。
- **#{} vs ${}**：`#{}` 生成 `?` 预编译占位符，MyBatis 传值，**防 SQL 注入**；`${}` 是字符串拼接有注入风险。项目里一律用 `#{}`。

#### ② empMapper.java —— 注解 + XML 混合（复杂 SQL 走 XML）

```java
@Mapper
public interface empMapper {
    // 复杂 SQL 方法只有声明，实现写在 empMapper.xml（同名同包同名）
    List<emp> list(empQueryPara empQueryPara);
    void deleteEmp(List<Integer> ids);
    emp selectEmp(Integer id);
    void updateEmp(emp emp);
    List<Map<String,Object>> countEmpJobData();
    List<Map<Object,Object>> getEmpGenderData();
    loginInfo loginNameAndPassword(emp emp);

    // 简单 SQL 用注解，顺便做主键回填
    @Options(useGeneratedKeys = true, keyProperty = "id")   // 【重点】新增后回填自增主键到 emp.id
    @Insert("INSERT INTO employee_info (username, NAME, ...) VALUES(#{userName}, #{name}, ...)")
    void insertExper(emp emp);
}
```

- **XML 对应规则**（笔记 07）：接口全限定名 `com.taile.mapper.empMapper` = XML 的 `namespace`；方法名 = XML 里 SQL 的 `id`；返回值类型对应 `resultType/resultMap`。
- **@Options(useGeneratedKeys=true, keyProperty="id")**：新增后把数据库自增主键回填进 `emp.id`。这是 13.4 里 `setEmpId(emp.getId())` 能拿到主键的前提。

#### ③ empMapper.xml —— 动态 SQL + resultMap（本项目最精华的部分）

```xml
<mapper namespace="com.taile.mapper.empMapper">   <!-- namespace = 接口全限定名，必填 -->

    <!-- ① resultMap + collection：一对多手动映射（员工 → 多条工作经历） -->
    <resultMap id="empResultMap" type="com.taile.pojo.emp">
        <result column="dept_name" property="deptName"/>
        <collection property="exprList" ofType="com.taile.pojo.empWork">  <!-- 一对多集合 -->
            <result column="emp_id" property="empId"/>
            <result column="company" property="company"/>
            <result column="begin_date" property="beginDate"/>
            <result column="end_date" property="endDate"/>
        </collection>
    </resultMap>
    <!-- 为什么用 resultMap：selectEmp 要一次带出多条工作经历，是"一"对"多"。
         普通驼峰映射只能映射单条记录，必须用 <collection> 才能装进 List<empWork>。 -->

    <!-- ② 动态更新：<set> 自动去尾逗号 + <if> 判断哪些字段要更新 -->
    <update id="updateEmp">
        UPDATE employee_info
        <set>
            <if test="userName != null and userName != ''"> username = #{userName}, </if>
            <if test="gender != null and gender != ''"> gender = #{gender}, </if>
            <!-- 更多字段同理 -->
        </set>
        WHERE id = #{id}
    </update>
    <!-- 好处：只更新前端传了值的字段，不误改其他列；<set> 自动去掉最后一个多余逗号 -->

    <!-- ③ 动态条件查询：<where> 自动去多余 and/or + <if> -->
    <select id="list" resultType="com.taile.pojo.emp">
        select e.id, e.username as user_name, e.name, e.gender, e.phone, e.job,
               e.salary, e.image, e.entry_date, e.dept_id, e.create_time, e.update_time,
               d.name as dept_name
        from employee_info as e
        left join dept as d on e.dept_id = d.id          <!-- 左外连接取部门名 -->
        <where>
            <if test="name != null and name != ''"> e.name like concat('%',#{name},'%') </if>
            <if test="gender != null"> and e.gender = #{gender} </if>
            <if test="start != null and end != null"> and e.entry_date between #{start} and #{end} </if>
        </where>
        order by e.create_time desc
    </select>
    <!-- concat('%',#{name},'%')：模糊查询用 concat 拼 %，避免用 ${} 直接拼导致注入 -->

    <!-- ④ 批量删除：<foreach> + IN -->
    <delete id="deleteEmp" parameterType="java.util.List">
        delete from employee_info where id in
        <foreach collection="ids" item="id" separator="," open="(" close=")">
            #{id}
        </foreach>
    </delete>

    <!-- ⑤ 报表聚合：数据库算好，前端直接画图 -->
    <select id="countEmpJobData" resultType="java.util.Map">
        select (case dept_id when 1 then '班主任' when 2 then '讲师'
                when 3 then '学工主管' when 4 then '咨询师' else '其他' end) as pos,
               count(*) as num
        from employee_info group by dept_id
    </select>
</mapper>
```

- **为什么查总数(count)用一条 SQL 也算**：报表场景由数据库聚合，返回 `Map`（SQL 别名即 key），**省掉为报表单独建实体类**（笔记 12 的报表技巧）。
- 这一节 = 笔记 07 的动态 SQL 四要素（`if/where/foreach/set`）+ 一对多 `resultMap` 全落地。

#### ④ empWorkMapper.xml —— 批量插入（foreach 多值）

```xml
<insert id="insert">
    insert into work_experience (emp_id, begin_date, end_date, company, job) values
    <foreach collection="list" item="expr" separator=",">
        (#{expr.empId},#{expr.beginDate},#{expr.endDate},#{expr.company},#{expr.job})
    </foreach>
</insert>
```

- 一次循环拼出多条 `(...),(...)` 插入，比一条条插高效，也正好接 13.4 的 `empWorkMapper.insert(exprList)` 批量插入。

---

### 13.6 JWT 认证（登录令牌）

#### ① JWTutils.java —— 生成 + 校验令牌

```java
public class JWTutils {
    private static final String secrete = "aXRoZWltYQ==";   // 签名密钥（硬编码，实际应放配置）
    private static Integer time = 1000*3600;                // 过期时间：1000ms*3600 = 1小时

    public static String generateJWT(Map<String,Object> claims) {
        return Jwts.builder()
                .signWith(SignatureAlgorithm.HS256, secrete)              // 签名算法+密钥
                .addClaims(claims)                                        // 放自定义数据(id/username)
                .setExpiration(new Date(System.currentTimeMillis() + time)) // 过期时间
                .compact();                                               // 生成令牌字符串
    }

    public static Claims parseJWT(String token) {
        return Jwts.parser().setSigningKey(secrete)  // 用同一密钥解析
                .parseClaimsJws(token).getBody();    // 签名被篡改/过期都会抛异常
    }
}
```

- `generateJWT` 生成、`parseJWT` 校验，**必须成对**用同一个密钥。
- **篡改 token 任意字符 → parse 抛异常 → 校验失败**；**过期也会抛异常**。这就是 JWT"防篡改 + 自动过期"的体现（笔记 09）。
- Base64 只是编码不是加密，所以 JWT 里**别放密码等敏感信息**，本项目只放了 id 和 username。

#### ② tokenInterceptor.java —— 登录校验核心（拦截器）

```java
@Slf4j
@Component      // 【注解】声明给容器，供 webConfig 注入注册
public class tokenInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        log.info("preHandle");
        String requestURI = request.getRequestURI();
        if (requestURI.contains("/login")) {   // 登录接口放行
            return true;
        }
        String token = request.getHeader("token");      // 前端每次请求把token放请求头
        if (token == null || token.isEmpty()) {         // 没带token → 拦截
            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);  // 401未认证
            return false;                               // false = 不放行
        }
        try {
            Claims claims = JWTutils.parseJWT(token);   // 校验签名+过期
            currentHolder.setCurrent((Integer) claims.get("id"));  // 【重点】存当前用户id
        } catch (Exception e) {
            log.info("token is invalid", e);
            return false;                               // 校验失败 → 拦截
        }
        return true;                                    // 通过 → 放行
    }
}
```

- **preHandle 返回 true 放行、false 拦截**——这就是拦截器控制请求进出的方式（笔记 09）。
- **为什么用 ThreadLocal 存用户 id**：拦截器的 `preHandle` 和切面 `operationLogAspect` 在**同一个请求线程**里先后执行。ThreadLocal 线程隔离，正好把"当前登录用户"一路带下去，日志切面才能知道"这次操作是谁干的"。
- **为什么校验放行都在 preHandle**：这是请求进入 Controller 前最后一次把关，放行/拦截在此决定。

#### ③ currentHolder.java —— ThreadLocal 容器

```java
public class currentHolder {
    private static ThreadLocal<Integer> contextHolder = new ThreadLocal<>();
    public static void setCurrent(Integer current) { contextHolder.set(current); }
    public static Integer getCurrent() { return contextHolder.get(); }
    public static void removeCurrent() { contextHolder.remove(); }  // 用后清除，防内存泄漏
}
```

- `set` 存、`get` 取、`remove` 清。**remove 很关键**：ThreadLocal 不清理会随着线程池线程复用累积旧数据（内存泄漏隐患），所以切面记完日志就 remove。

#### ④ tokenFilter.java —— 被注释的 Filter 版本（对照用）

```java
//@WebFilter(urlPatterns = "/*")   // 被注释：现在用拦截器方案，Filter 留作对照
@Slf4j
public class tokenFilter implements Filter {
    // 逻辑和 tokenInterceptor 基本一样：查url→放行login→查token→401→校验→放行
}
```

- **对照笔记 09**：Filter 是 Servlet 规范（`@WebFilter` + 启动类 `@ServletComponentScan`），拦截**所有资源**；Interceptor 是 Spring 的，只拦 **Controller**。项目最终选了 Interceptor（更贴合 Spring 生态，能拿到 handler/更细控制）。

---

### 13.7 AOP 操作日志（切面编程，Spring 第二大核心落地）

#### ① logOperation.java —— 自定义"标记注解"

```java
@Target(ElementType.METHOD)          // 【注解1】只能标在方法上
@Retention(RetentionPolicy.RUNTIME)  // 【注解2】运行期仍保留
public @interface logOperation {
}
```

- **【注解1】@Target(METHOD)**：限制这个注解只能用在方法上，用在类/字段上会报错。
- **【注解2】@Retention(RUNTIME)**：**最关键**。默认 Retention 是 CLASS，编译后注解信息会被丢。必须改成 RUNTIME，切面才能在**运行时通过反射读到这个注解**去匹配方法。不写它，AOP 匹配不到。
- 注解体是空的 → 纯"标记"，不加任何配置。

#### ② operationLogAspect.java —— 切面：自动记操作日志

```java
@Slf4j
@Aspect            // 【注解1】声明这是切面类
@Component         // 【注解2】交给容器
public class operationLogAspect {
    @Autowired
    private operateLogMapper operateLogMapper;

    @Around("@annotation(com.taile.anno.logOperation)")   // 【重点1】切入点
    public Object logOperation(ProceedingJoinPoint jointPoint) throws Throwable {
        long start = System.currentTimeMillis();
        Object proceed = jointPoint.proceed();   // 【代码1】执行原方法（放行）
        long end = System.currentTimeMillis();

        operateLog ol = new operateLog();
        Integer userId = getCurrentUser();       // 从 ThreadLocal 拿当前登录用户id
        ol.setOperateEmpId(userId);
        ol.setOperatetime(LocalDateTime.now().withNano(0));
        ol.setClassName(jointPoint.getTarget().getClass().getName());  // 哪个类
        ol.setMethodName(jointPoint.getSignature().getName());         // 哪个方法
        ol.setMethodParams(Arrays.toString(jointPoint.getArgs()));     // 传了什么参数
        ol.setReturnValue(proceed.toString());                         // 返回什么
        ol.setCostTime(end - start);                                   // 耗时
        operateLogMapper.insert(ol);   // 写进 operation_log 表

        currentHolder.removeCurrent();  // 用完清 ThreadLocal，防内存泄漏
        return proceed;                 // 【代码2】把原方法返回值原样返回
    }
    public static Integer getCurrentUser() { return currentHolder.getCurrent(); }
}
```

- **【重点1】@Around("@annotation(com.taile.anno.logOperation)")**：切入点表达式，意思是"拦截所有加了 `@logOperation` 注解的方法"。这就是 13.3 里 empController 每个方法标 `@logOperation` 的原因——**注解当开关，切面当执行器**。
- **【代码1】proceed()**：放行去执行被拦截的原始方法。**必须先调它**，原业务才跑得起来。
- **【代码2】必须 return proceed**：`@Around` 的返回值会被当作原方法的返回值返回给调用方。不 return，前端就拿不到数据（返回 null）。
- **为什么用 @Around 而不是 @Before/@After**：要记"耗时 = 结束后 - 开始前"，只有 @Around 能同时包住前后，还能拿返回值。这是 @Around"最强大"的体现。
- **整体价值**：一行业务代码都没改 empController，就自动给所有增删改查加了"操作日志"功能——**AOP 无侵入**（笔记 10）的最佳例证。切面需要的日志字段，全从 `jointPoint`（方法信息）+ `currentHolder`（用户id）拿到。

---

### 13.8 阿里云 OSS 上传（云存储落地）

#### ① utils.java —— @ConfigurationProperties 配置绑定

```java
@Data
@Component                                          // 【注解1】交给容器
@ConfigurationProperties(prefix = "aliyun.oss")     // 【注解2】自动绑定yml里aliyun.oss.*前缀
public class utils {
    private String endPoint;    // 自动 = https://oss-cn-beijing.aliyuncs.com
    private String bucketName;  // 自动 = srqxx-web
    private String region;      // 自动 = cn-beijing
}
```

- **【注解2】@ConfigurationProperties**：前缀对得上 yml 的 `aliyun.oss.*`，字段名匹配就自动注入。**好处**：不用一个个 `@Value` 写，配置集中、易维护——正是笔记 08 说的企业级写法。

#### ② AliyunOSSOperator.java —— 上传核心

```java
@Component
public class AliyunOSSOperator {
    @Autowired
    private utils ossConfig;   // 注入配置（从容器拿，不 new）

    public String upload(byte[] content, String originalFilename) throws Exception {
        // 凭证从环境变量读，避免密钥写死在代码里（安全）
        EnvironmentVariableCredentialsProvider credentialsProvider =
            CredentialsProviderFactory.newEnvironmentVariableCredentialsProvider();

        // 按日期分目录 yyyy/MM，避免所有文件堆一个文件夹
        String dir = LocalDate.now().format(DateTimeFormatter.ofPattern("yyyy/MM"));
        // UUID 重命名，避免重名覆盖原图
        String newFileName = UUID.randomUUID() + originalFilename.substring(originalFilename.lastIndexOf("."));
        String objectName = dir + "/" + newFileName;

        OSS ossClient = ...build();   // 建 OSS 客户端（省去细节）
        try {
            ossClient.putObject(bucketName, objectName, new ByteArrayInputStream(content));  // 上传
        } finally {
            ossClient.shutdown();    // 用完关客户端，finally 保证一定关
        }
        return endPoint.split("//")[0] + "//" + bucketName + "." + endPoint.split("//")[1] + "/" + objectName;
        // 拼完整可访问URL：协议//桶名.域名/路径
    }
}
```

- **为什么 UUID 重命名**：用户传的可能是重名文件（如 `a.png`），直接存会覆盖别人的。UUID 保证唯一。
- **为什么按日期分目录**：文件按 `yyyy/MM` 归到不同文件夹，便于管理、避免单目录文件过多。
- **为什么 finally shutdown**：OSS 客户端占用连接资源，必须关闭。`finally` 保证**即使上传抛异常也会关闭**，不泄漏资源。
- **凭证读环境变量**：`OSS_ACCESS_KEY_ID / OSS_ACCESS_KEY_SECRET`，不写死在代码/配置文件（安全最佳实践）。

#### ③ uploadController.java

```java
@PostMapping("/upload")
public Result load(MultipartFile file) throws Exception {
    log.info("上传文件:{}", file.getOriginalFilename());
    String upload = ayop.upload(file.getBytes(), file.getOriginalFilename());  // 字节流转给OSS
    return Result.success(upload);   // 返回图片URL，前端存进 emp.image
}
```

- 前端 `FormData` 以 multipart 传 → `MultipartFile` 接住 → `getBytes()` 给 OSS → 返回 URL。**不再写本地磁盘**（对应笔记 08 云存储思路）。

---

### 13.9 一条完整请求串起来（面试怎么讲）

用户点"查询员工列表"，背后发生了什么：

```
浏览器带token → GET /emps
    ↓ ① tokenInterceptor.preHandle：校验token合法，把user id存进ThreadLocal → 放行
    ↓ ② empController.list（标了@logOperation）
          → operationLogAspect 环绕，开始计时
    ↓ ③ empService.list → PageHelper分页
    ↓ ④ empMapper.list → XML动态SQL带条件查库（left join dept 取部门名）
    ↓ ⑤ 结果原路返回，切面把操作日志insert进operation_log，清空ThreadLocal
    ↓ ⑥ 浏览器收到 Result{ code:1, data: pageResult{total, rows} }
```

这就是 **三层架构 + 拦截器(JWT) + AOP(日志) + MyBatis(动态SQL) + PageHelper(分页)** 的完整协作，也是整个笔记 06~10 的综合演练。

---

### 13.10 本项目的"反面教材"（面试/进阶可主动提）

1. **updateEmp 删经历有 bug**（`empServiceImpl.java`）：删除工作经历的条件是 `exprList 非空` 才删，且方法没加 `@Transactional`。→ 若用户把经历清空再保存，旧记录删不掉。**应改成"无条件先删后插 + 加事务"**。
2. **empMapper.xml 的 updateEmp** 里 `update_time` 写了两个互斥 `<if>`（一个判 null 一个判非 null），结果相同，冗余。→ 留一个即可。
3. **JWT 密钥硬编码**在 `JWTutils.java`。→ 应放配置文件/环境变量，避免泄露。
4. **tokenInterceptor 校验失败只 return false**，没写响应体（返回 200 空响应）。→ 前端拿不到具体错误，应写 JSON 错误体 + 401 状态码。
5. `operateLog.java` 有个多余的 `import net.sf.jsqlparser...`（没用上），应删。

> 面试主动指出自己项目能优化的点，比"项目完美无缺"更有说服力。

---

## ★ 完整知识图谱

```
JavaWeb ─┬─ 前端基础 ── HTML(结构) / CSS(表现) / JS(行为)
         ├─ JS+Vue ──── 指令(v-for/bind/if/model/on) / 生命周期 / Axios
         ├─ 后端基础 ── Maven / HTTP / SpringBoot / 三层架构
         ├─ Spring核心 ─┬─ IOC(容器管理对象) + DI(依赖注入)
         │              └─ AOP(面向切面编程)
         ├─ 持久层 ──── MyBatis(注解/XML/动态SQL) + 连接池
         ├─ 数据库 ──── 多表查询 / 事务 / 索引
         ├─ 项目实战 ── 部门管理 / 员工管理 / 登录认证(JWT)
         │             ├─ 参数接收 / 全局异常 / 文件上传
         │             └─ Filter / Interceptor
         ├─ 前端工程化 ── VueRouter / ElementPlus / axios封装
         └─ 部署 ────── Linux / Docker(镜像+容器) / DockerCompose
```

## ★ 面试八股速记

**1. Spring 两大核心？**
> IOC 控制反转（对象创建交给容器）+ AOP 面向切面编程（不改代码增强功能）。

**2. @Autowired 与 @Resource 区别？**
> @Autowired（Spring）按类型；@Resource（JDK）按名称。

**3. 多个同类型 Bean 注入怎么办？**
> @Primary（默认）/ @Qualifier（指定名称）/ @Resource(name=)。

**4. GET 与 POST 区别？**
> GET 参数在 URL、有长度限制、不安全；POST 参数在请求体、无限制、较安全。

**5. #{} 与 ${} 区别？**
> #{} 预编译占位符，防 SQL 注入，推荐；${} 字符串拼接，有注入风险。

**6. v-if 与 v-show 区别？**
> v-if 条件渲染（创建/移除节点）；v-show 始终渲染（控制 display），频繁切换用 v-show。

**7. Spring 事务默认回滚什么异常？**
> 默认只回滚 RuntimeException；要全回滚用 `@Transactional(rollbackFor=Exception.class)`。

**8. REQUIRED 与 REQUIRES_NEW？**
> REQUIRED 有事务就加入；REQUIRES_NEW 新建独立事务（适合日志场景）。

**9. Cookie / Session / JWT 对比？**
> Cookie 存客户端；Session 存服务端（集群不能用）；JWT 令牌主流，支持移动端+集群。

**10. Filter 与 Interceptor 区别？**
> Filter 拦截所有资源（实现 Filter 接口）；Interceptor 只拦截 Spring 的 Controller（实现 HandlerInterceptor）。

**11. 事务四大特性 ACID？**
> 原子性、一致性、隔离性、持久性。

**12. AOP 底层原理？**
> 动态代理：运行时为目标对象生成代理对象增强功能。

**13. 为什么用云存储不用本地磁盘？**
> 本地不安全（磁盘损坏丢失）、容量有限、无法直接访问。

**14. 物理外键 vs 逻辑外键？**
> 物理外键影响性能、不适合分布式、易死锁，企业用逻辑外键（业务层维护）。
