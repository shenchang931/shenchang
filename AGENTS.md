# AGENTS.md

## 项目概述

《高级网页设计》课程练习集，全部为原生 HTML/CSS/JS 静态页面，无任何构建步骤、包管理器或外部依赖。

## 结构

- `index.html` — 作品集导航页，链接到所有练习页面
- `N-M.html` — 各章节练习（如 `3-1form.html`、`6-1 flex.html`、`10-3.html`）
- `img/` — 练习页面引用的图片资源
- `font/` — iconfont 图标资源目录
- `WorkBuddy/` — 预构建的 Electron 应用，非课程练习代码，不要修改

## 约定

- 每个 HTML 页面使用内联 `<style>` 和 `<script>`，无需构建或外部 CSS/JS 文件
- 部分文件名含中文字符（如 `5-1 层叠与继承.html`），在 `index.html` 链接中使用 URL 编码（如 `5-1%20%E5%B1%82%E5%8F%A0%E4%B8%8E%E7%BB%A7%E6%89%BF.html`）
- 无 lint、typecheck 或测试 — 直接用浏览器打开即可验证
- 部分页面使用 `img/` 中的相对路径图片，从根目录提供文件即可正常显示
- 代码注释和 UI 文本使用中文

## 项目规则

### 命名规范

所有 CSS 类名、id 名、JS 变量名必须以 `sc_` 开头，使用小写字母 + 下划线分隔：

- CSS 类名示例：`sc_container`、`sc_btn`、`sc_card_active`
- id 示例：`sc_main`、`sc_header`、`sc_modal`
- JS 变量示例：`sc_count`、`sc_isVisible`、`sc_handleClick`
- JS 函数示例：`sc_init`、`sc_updateView`、`sc_formatDate`

禁止使用无前缀的旧写法（如 `container`、`main`、`count`），新建代码一律遵循 `sc_` 前缀。存量页面中的 `shen_` 前缀代码暂保留，改动到对应代码时同步更新为 `sc_`。

### 页面注释

所有 HTML 页面顶部必须包含注释，注明姓名、日期和功能名称，格式：

```
<!-- 姓名：沈畅，日期：2026-10-22，功能名称：页面功能名称 -->
```

- 日期：页面创建或最后一次功能性修改的日期，格式 `yyyy-mm-dd`
- 功能名称：该页面实现的功能或练习标题

### 技术栈

- 只使用原生 HTML、CSS 和 JavaScript 实现，禁止引入任何第三方库、框架和 CDN 资源
- 图标统一使用 `font/` 目录下的 iconfont 图标
- 需要获取服务器端数据时，使用 `fetch` API

### 首页导航

新建的 HTML 练习页面必须同步添加到 `index.html` 的作品导航区域，包含卡片链接、序号、标题、文件名、描述和标签。

### 提示词记录

每次执行任务前，必须将提示词以追加方式写入 `shen_prompt.md`（不覆盖已有内容）。格式如下：

```
---
## 2026-10-12-14-30
**模型：** opencode/longcat-2.5-preview-free
**提示词：** <用户原始提示词内容>
---
```

字段说明：
- 时间：格式 `yyyy-mm-dd-hh-mm`，使用 24 小时制
- 模型：填写实际使用的模型 ID
- 提示词：完整记录用户发送的原始文本，不做删减
