---
title: Fullstack 完整流程
published: 2025-11-15
tags: [Markdown, Blogging, Demo]
category: Examples
draft: true
---

# 🌟 全栈登录注册系统完整流程（含命令行步骤 + MySQL 创建）

> 技术栈：Node.js + Express + MySQL + HTML/CSS/JS  
> 本文档适用于完全新手，包含所有命令、路径、注意事项  
> 可直接保存、作为作业附录或复习笔记使用喵～

---

# 1️⃣ 创建项目目录

随便一个地方创建一个文件夹（名字随便，例如）：

```
MyWebApp/
```

进入项目根目录：

```bash
cd MyWebApp
```

所有 node 相关命令必须在这个目录执行。

---

# 2️⃣ 初始化 Node.js 项目（必须执行）

```bash
npm init -y
```

生成：

```
package.json
```

---

# 3️⃣ 安装依赖（express + cors + mysql2）

必须在项目根目录执行：

```bash
npm install express cors mysql2
```

---

# 4️⃣ 创建后端文件 server.js

路径：

```
MyWebApp/server.js
```

内容：

```js
import mysql from "mysql2";
import express from "express";
import cors from "cors";

const app = express();
app.use(cors());
app.use(express.json());

// ⭐ 连接数据库
const db = mysql.createConnection({
  host: "localhost",
  user: "root",
  password: "123456",
  database: "webapp"
});

db.connect(err => {
  if (err) console.error("数据库连接失败：", err);
  else console.log("数据库连接成功！");
});

// ⭐ 注册接口
app.post("/register", (req, res) => {
  const { username, password } = req.body;
  const sql = "INSERT INTO users (username, password) VALUES (?, ?)";
  db.query(sql, [username, password], err => {
    if (err) res.json({ success: false, error: err });
    else res.json({ success: true });
  });
});

// ⭐ 登录接口
app.post("/login", (req, res) => {
  const { username, password } = req.body;
  const sql = "SELECT * FROM users WHERE username=? AND password=?";
  db.query(sql, [username, password], (err, result) => {
    if (err) return res.json({ success: false, error: err });

    if (result.length > 0) res.json({ success: true });
    else res.json({ success: false });
  });
});

// ⭐ 启动后端
app.listen(3001, () => {
  console.log("后端已运行：http://localhost:3001/");
});
```

---

# 5️⃣ 创建前端目录 public

在项目根目录创建：

```
MyWebApp/public/
```

---

# 6️⃣ login.html（放在 public 目录）

```
MyWebApp/public/login.html
```

```html
<input id="username" placeholder="username">
<input id="password" type="password" placeholder="password">
<button onclick="doLogin()">Login</button>

<script>
async function doLogin() {
  const username = document.getElementById("username").value;
  const password = document.getElementById("password").value;

  const res = await fetch("http://localhost:3001/login", {
    method: "POST",
    headers: {"Content-Type":"application/json"},
    body: JSON.stringify({ username, password })
  });

  const data = await res.json();

  if (data.success) {
    alert("登录成功！");
    window.location.href = "/dashboard.html";
  } else {
    alert("用户名或密码错误！");
  }
}
</script>
```

---

# 7️⃣ register.html（放在 public 目录）

```
MyWebApp/public/register.html
```

```html
<input id="username" placeholder="username">
<input id="password" placeholder="password">
<button onclick="doRegister()">Register</button>

<script>
async function doRegister() {
  const username = document.getElementById("username").value;
  const password = document.getElementById("password").value;

  const res = await fetch("http://localhost:3001/register", {
    method: "POST",
    headers: {"Content-Type":"application/json"},
    body: JSON.stringify({ username, password })
  });

  const data = await res.json();

  if (data.success) {
    alert("注册成功！");
    window.location.href = "/login.html";
  } else {
    alert("注册失败！");
  }
}
</script>
```

---

# 8️⃣ 创建 MySQL 数据库与表（在 MySQL 命令行执行）

```sql
CREATE DATABASE webapp;
USE webapp;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(255),
  password VARCHAR(255)
);
```

---

# 9️⃣ 启动后端（必须在 MyWebApp/ 根目录运行）

```bash
node server.js
```

看到：

```
数据库连接成功！
后端已运行：http://localhost:3001/
```

表示一切正常。

---

# 🔟 访问前端页面

直接打开本地文件：

```
MyWebApp/public/login.html
```

或 VS Code 使用 Live Server 打开也可。

不需要任何服务器即可显示页面。

---

# 1️⃣1️⃣ 测试 API（可选）

登录：

```bash
curl -X POST http://localhost:3001/login \
  -H "Content-Type: application/json" \
  -d '{"username":"stars","password":"123456"}'
```

注册：

```bash
curl -X POST http://localhost:3001/register \
  -H "Content-Type: application/json" \
  -d '{"username":"newuser","password":"abc"}'
```

---

# 1️⃣2️⃣ 最终项目结构

```
MyWebApp/
 ├── server.js
 ├── package.json
 ├── node_modules/
 ├── public/
 │    ├── login.html
 │    ├── register.html
 │    └── dashboard.html（可选）
```

---

# 1️⃣3️⃣ 整体运行流程（超简总结）

```
1. cd MyWebApp
2. npm install express cors mysql2
3. node server.js       ← 启动后端
4. 打开 public/login.html
5. 登陆或注册
```

---

# 🎉 恭喜主人！  
这是一个可以当作“真正全栈工程模板”的完整流程喵～
