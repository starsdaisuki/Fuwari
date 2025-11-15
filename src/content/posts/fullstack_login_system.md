---
title: 登录注册系统含 MySQL 创建）
published: 2025-11-15
tags: [Markdown, Blogging, Demo]
category: Examples
draft: true
---

# 全栈登录注册系统完整流程（含 MySQL 创建）Markdown 版

## 1️⃣ 创建数据库与数据表（MySQL）

```sql
-- 创建数据库
CREATE DATABASE webapp;

-- 使用数据库
USE webapp;

-- 创建用户表
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(255) NOT NULL,
  password VARCHAR(255) NOT NULL
);
```

---

## 2️⃣ Node.js 连接 MySQL

```js
import mysql from "mysql2";
import express from "express";
import cors from "cors";

const app = express();
app.use(cors());
app.use(express.json());

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
```

---

## 3️⃣ 注册接口 /register

```js
app.post("/register", (req, res) => {
  const { username, password } = req.body;

  const sql = "INSERT INTO users (username, password) VALUES (?, ?)";
  db.query(sql, [username, password], err => {
    if (err) res.json({ success: false, error: err });
    else res.json({ success: true });
  });
});
```

---

## 4️⃣ 登录接口 /login

```js
app.post("/login", (req, res) => {
  const { username, password } = req.body;

  const sql = "SELECT * FROM users WHERE username=? AND password=?";
  db.query(sql, [username, password], (err, result) => {
    if (err) return res.json({ success: false, error: err });

    if (result.length > 0) res.json({ success: true });
    else res.json({ success: false });
  });
});
```

---

## 5️⃣ 启动后端

```js
app.listen(3001, () => {
  console.log("后端已运行：http://localhost:3001/");
});
```

启动：

```bash
node server.js
```

---

## 6️⃣ login.html 前端页面

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
    alert("登录成功喵！");
    window.location.href = "/dashboard.html";
  } else {
    alert("用户名或密码错误喵！");
  }
}
</script>
```

---

## 7️⃣ register.html 注册页面

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

## 8️⃣ 文件结构

```
project/
 ├── server.js
 ├── public/
 │    ├── login.html
 │    ├── register.html
 │    └── dashboard.html
 └── package.json
```

---

## 9️⃣ 系统流程图

```
前端 HTML
     ↓ fetch()
Node.js 后端 (Express)
     ↓ SQL
MySQL 数据库
     ↓
Node.js JSON 返回
     ↓
前端跳转 + 提示
```
