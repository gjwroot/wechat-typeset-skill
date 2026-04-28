---
name: "wechat-typeset"
description: "将用户发送的文章内容转换为微信公众号排版 HTML 文件。当用户发送文章内容并要求转微信排版、公众号排版时调用此技能。"
---

# 微信公众号排版转换技能

## 触发条件

当用户发送文章/新闻/教程等内容，并要求以下任一操作时触发：
- "转微信排版" / "转微信公众号排版"
- "转微信" / "公众号排版"
- "帮我排版" / "转换排版"

## 微信 HTML 规则

**微信编辑器标签支持情况：**
- ✅ 安全可用：`<section>`, `<p>`, `<span>`, `<br>`, `<strong>`, `<b>`, `<em>`, `<i>`, `<blockquote>`, `<code>`, `<ul>`, `<ol>`, `<li>`, `<table>`, `<thead>`, `<tbody>`, `<tr>`, `<td>`, `<h3>`~`<h5>`
- ⚠️ 有限支持：`<div>`（会被转成 `<p>`）、`<h1>`/`<h2>`（会被转成 `<p>`）、`<a>`（仅支持 mp.weixin.qq.com 域名链接）、`<img>`（需用微信图床地址）
- ❌ 禁止：`<style>`, `class`, `<script>`, `<iframe>`

**本技能的设计选择：**
为确保最大兼容性和一致的渲染效果，本技能采用**保守策略**——仅使用 `<section>`、`<p>`、`<span>` 三个标签 + 纯内联样式。原因：
1. `<section>` 是微信编辑器自身大量使用的容器标签，兼容性最佳
2. `<div>` 会被微信自动转成 `<p>`，导致样式丢失
3. `<h1>`/`<h2>` 会被转成 `<p>`，标题样式不可控
4. `<a>` 外链会被过滤或触发安全提醒
5. `<img>` 需微信图床地址，无法在 HTML 中直接使用外部图片
6. `<strong>`/`<em>` 虽可用，但用 `<span style="font-weight:bold;">` 更灵活可控

**内容处理规则：**
- 链接 → 纯文本 URL，格式：`<p style="margin:0 0 8px 0;color:#64748b;font-size:13px;">🔗 URL</p>`
- 图片 → 占位符：`<p style="margin:0;text-align:center;color:#94a3b8;font-size:14px;">【演示效果】</p>` 后跟空行占位 `<p style="margin:0;">&nbsp;</p><p style="margin:0;">&nbsp;</p>`
- 代码块内 HTML → 必须转义：`<` → `&lt;`, `>` → `&gt;`, `&` → `&amp;`
- 加粗 → `<span style="font-weight:bold;">文字</span>`
- 斜体 → `<span style="font-style:italic;">文字</span>`

## 排版样式规范

### 基础样式
```html
<body style="margin:0;padding:20px 16px;background:#fff;color:#333;font-family:-apple-system,BlinkMacSystemFont,'PingFang SC','Microsoft YaHei','Helvetica Neue',sans-serif;font-size:15px;line-height:1.8;">
```

### 顶部渐变标题栏
```html
<section style="margin:0 0 24px 0;padding:24px 20px;background:linear-gradient(135deg,#1e293b 0%,#334155 100%);border-radius:12px;">
  <p style="margin:0 0 6px 0;font-size:22px;font-weight:bold;color:#ffffff;">标题文字</p>
</section>
```

### 章节标题（H2）— 依次循环 7 种配色
```html
<!-- 第1个章节：蓝色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#dbeafe;border-radius:8px;border-left:4px solid #3b82f6;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#1e40af;">章节标题</p>
</section>

<!-- 第2个章节：绿色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#d1fae5;border-radius:8px;border-left:4px solid #10b981;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#065f46;">章节标题</p>
</section>

<!-- 第3个章节：紫色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#ede9fe;border-radius:8px;border-left:4px solid #8b5cf6;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#5b21b6;">章节标题</p>
</section>

<!-- 第4个章节：橙色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#ffedd5;border-radius:8px;border-left:4px solid #f97316;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#9a3412;">章节标题</p>
</section>

<!-- 第5个章节：粉色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#fce7f3;border-radius:8px;border-left:4px solid #ec4899;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#9d174d;">章节标题</p>
</section>

<!-- 第6个章节：靛蓝 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#e0e7ff;border-radius:8px;border-left:4px solid #6366f1;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#3730a3;">章节标题</p>
</section>

<!-- 第7个章节：黄色 -->
<section style="margin:0 0 12px 0;padding:10px 16px;background:#fef3c7;border-radius:8px;border-left:4px solid #f59e0b;">
  <p style="margin:0;font-size:17px;font-weight:bold;color:#92400e;">章节标题</p>
</section>
```

### 🔥🔥 重磅头条卡片（红色渐变）
```html
<section style="margin:0 0 20px 0;padding:16px;background:linear-gradient(135deg,#fef2f2 0%,#fee2e2 100%);border-radius:10px;border:1px solid #fca5a5;">
  <p style="margin:0 0 8px 0;font-size:18px;font-weight:bold;color:#991b1b;">🔥🔥 标题文字</p>
  <p style="margin:0 0 8px 0;color:#333;">正文摘要内容</p>
  <p style="margin:0 0 4px 0;color:#64748b;font-size:13px;">🔗 链接URL</p>
  <p style="margin:0;color:#94a3b8;font-size:12px;">💡 编辑点评内容</p>
</section>
```

### 🔥 重要话题卡片（黄色渐变）
```html
<section style="margin:0 0 20px 0;padding:16px;background:linear-gradient(135deg,#fffbeb 0%,#fef3c7 100%);border-radius:10px;border:1px solid #fcd34d;">
  <p style="margin:0 0 8px 0;font-size:17px;font-weight:bold;color:#92400e;">🔥 标题文字</p>
  <p style="margin:0 0 8px 0;color:#333;">正文摘要内容</p>
  <p style="margin:0 0 4px 0;color:#64748b;font-size:13px;">🔗 链接URL</p>
</section>
```

### 代码块
```html
<section style="margin:0 0 16px 0;padding:16px;background:#1e293b;border-radius:8px;overflow-x:auto;">
  <p style="margin:0 0 8px 0;padding:8px 14px;background:rgba(255,255,255,0.06);"><span style="font-size:11px;color:#94a3b8;font-family:Menlo,Monaco,Consolas,monospace;">CODE</span></p>
  <p style="margin:0;font-size:13px;color:#e2e8f0;font-family:Menlo,Monaco,Consolas,monospace;line-height:1.6;white-space:pre-wrap;word-break:break-all;">转义后的代码内容</p>
</section>
```

### 普通段落
```html
<p style="margin:0 0 16px 0;">段落内容</p>
```

### 列表项（用 p 标签 + 圆点）
```html
<p style="margin:0 0 8px 0;">• 列表项内容</p>
<p style="margin:0 0 8px 0;">&nbsp;&nbsp;&nbsp;&nbsp;- 子项内容</p>
```

### 引用块
```html
<section style="margin:0 0 16px 0;padding:12px 16px;background:#f8fafc;border-radius:8px;border-left:4px solid #3b82f6;">
  <p style="margin:0;color:#475569;font-size:14px;">引用内容</p>
</section>
```

### 分隔线
```html
<section style="margin:20px 0;border:none;border-top:1px solid #e2e8f0;"></section>
```

### 结尾
```html
<p style="text-align:center;margin:20px 0 0 0;"><span style="font-size:12px;color:#ccc;">— END —</span></p>
```

## 文章类型识别与排版策略

### 1. 每日早报/综合新闻
- 顶部渐变标题栏（含日期）
- 🔥🔥 重磅头条 → 红色卡片
- 🔥 重要话题 → 黄色卡片
- ⭐ 头条精选 → 蓝色章节标题
- 各主题分区（科技/财经/社会/开源等）→ 依次用不同颜色章节标题
- 💡 编辑推荐 → 黄色章节标题
- 结尾 — END —

### 2. 财经早报
- 顶部渐变标题栏 💼
- 🔥🔥 重磅头条 → 红色卡片（含💡深度点评）
- ⭐ 头条精选 → 蓝色章节标题
- 📈 市场动态 → 绿色章节标题
- 🏢 公司新闻 → 紫色章节标题
- 💰 投资观点 → 橙色章节标题
- 结尾 — END —

### 3. 技术教程
- 顶部渐变标题栏 📖
- 📋 前置要求 / 概述 → 蓝色章节标题
- 🚀 快速开始（步骤）→ 绿色章节标题
- 📝 核心概念 → 紫色章节标题
- 🔧 实战示例（含代码块）→ 橙色章节标题
- ❓ 常见问题 → 粉色章节标题
- 🔗 相关资源 → 靛蓝章节标题
- 结尾 *觉得有用？给个 Star ⭐*

### 4. 开源项目推荐
- 顶部渐变标题栏 🌟
- 🔥🔥 项目卡片 → 红色卡片（含语言/协议/Star数）
- 🔥 项目卡片 → 黄色卡片
- 📊 本周趋势 → 蓝色章节标题
- 💡 编辑推荐 → 黄色章节标题
- 结尾 *发现更多好项目？欢迎推荐 ⭐*

### 5. 通用文章
- 顶部渐变标题栏
- 按内容自然分段，章节标题依次循环 7 种配色
- 代码块用深色背景
- 链接用纯文本
- 结尾 — END —

## 输出规范

1. 文件名格式：`{主题描述}-wechat.html`（如 `fluid-interaction-wechat.html`）
2. 文件保存到用户工作区
3. 生成后用 Grep 验证无违规标签：`<style>`, `class=`, `<div`, `<h[1-6]`, `<ul`, `<ol`, `<li`, `<table`, `<a `, `<img `, `<strong`, `<em`
4. 验证代码块内 HTML 已转义：检查 `&lt;`, `&gt;`, `&amp;`
