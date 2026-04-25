# xmov

基于魔珐星云 (Xmov) SDK 的文化遗产数字守护者 Demo，使用数字人进行文化遗产知识讲解与交互问答。

## 项目结构

```
xmov/
├── index.html                        # 主 Demo（单文件，浏览器直接打开）
├── README.md
└── .codeartsdoer/                    # CodeArts 智能体配置
    └── skills/xmov/SKILL.md          # SDK 集成专家 Skill 定义
```

## 功能特性

- **数字人讲解** — 连接魔珐星云 SDK，数字人实时语音播报文化遗产知识
- **导览模式** — 按分类自动逐条讲解，支持停止/继续
- **交互问答** — 输入问题即时回答，提问自动打断导览
- **分类筛选** — 建筑/石窟/陵墓/自然/非遗五大分类
- **自定义字幕** — 拦截 SDK 默认字幕，金色大字显示在数字人下方
- **背景图层** — 通过 `widget_pic` 事件动态切换数字人背景
- **横竖屏切换** — 一键切换数字人画布比例
- **大屏适配** — 左右分栏布局，字体放大，适合展示屏/大屏使用

## 快速开始

1. 在浏览器中打开 `index.html`
2. 在顶部输入框填入魔珐星云平台的 **App ID** 和 **App Secret**
3. 点击 **连接** 按钮，等待数字人加载完成
4. 选择文化遗产或点击 **导览** 开始自动讲解

## 技术架构

### 布局

```
┌──────────────────────────────────────────────┐
│  配置栏 (AppID / Secret / 连接 / 状态)       │
├─────────────────────┬────────────────────────┤
│                     │  文化遗产              │
│                     │  ┌──────────────────┐  │
│    数字人舞台        │  │ 分类标签          │  │
│    (XmovAvatar)     │  │ 遗产选择器        │  │
│                     │  ├──────────────────┤  │
│                     │  │ 详情（可滚动）    │  │
│                     │  │  - 历史沿革       │  │
│    ┌─────────────┐  │  │  - 核心知识       │  │
│    │  字幕       │  │  │  - 文化故事       │  │
│    └─────────────┘  │  ├──────────────────┤  │
│                     │  │ 提问 | 导览       │  │
└─────────────────────┴────────────────────────┘
```

### SDK 集成规范

遵循 `.codeartsdoer/skills/xmov/SKILL.md` 中定义的 R-C-R-C 规范：

| 规范 | 实现 |
|------|------|
| 配置解耦 | AppID/Secret 通过 DOM 输入，不硬编码 |
| proxyWidget | `widget_pic`（背景）、`subtitle_on`/`subtitle_off`（字幕） |
| 语音打断 | `interactiveidle()` → `onVoiceStateChange` → `speak()` |
| 流式播报 | `speak(content, is_start, is_end)` 分段播报 |
| 生命周期 | `beforeunload` → `sdk.destroy()` 防止 Token 泄漏 |
| 进度条 | `onDownloadProgress` 仅 `<100` 时更新 |

### 模式状态机

```
idle (待命) ──点击导览──→ tour (导览中) ──点击提问──→ quiz (问答)
    ↑                        │                        │
    └──导览结束──────────────┘                        │
    ↑                                                 │
    └─────────────────────────────────────────────────┘
```

- **提问自动打断导览**：点击提问时清除所有导览定时器，切换为问答模式
- **导览按钮动态切换**：导览中显示"停止"，可随时中断

## 文化遗产内容

涵盖 10 处中国世界文化遗产/非遗：

| 名称 | 分类 | 列入年份 |
|------|------|---------|
| 长城 | 建筑 | 1987 |
| 故宫 | 建筑 | 1987 |
| 兵马俑 | 陵墓 | 1987 |
| 布达拉宫 | 建筑 | 1994 |
| 莫高窟 | 石窟 | 1987 |
| 苏州园林 | 建筑 | 1997 |
| 龙门石窟 | 石窟 | 2000 |
| 明孝陵 | 陵墓 | 2003 |
| 黄山 | 自然 | 1990 |
| 昆曲 | 非遗 | 2001 |

每处遗产包含：年代、描述、历史沿革、核心知识（4条）、文化故事。

## 相关链接

- **魔珐星云官网** — [https://www.xingyun3d.com](https://www.xingyun3d.com)
- **魔珐星云开放平台** — [https://open.xingyun3d.com](https://open.xingyun3d.com)
- **魔珐星云 SDK 文档** — [https://docs.xingyun3d.com](https://docs.xingyun3d.com)
- **在线 Demo** — [https://yeatsliao.github.io/xmov-cultural-guardian/](https://yeatsliao.github.io/xmov-cultural-guardian/)

## 依赖

- **魔珐星云 SDK** — `https://media.xingyun3d.com/xingyun3d/general/litesdk/xmovAvatar@latest.js`
- 无其他依赖，单 HTML 文件即可运行

## 环境要求

- 现代浏览器（Chrome / Edge / Firefox / Safari）
- 魔珐星云平台账号（App ID + App Secret）
- 网络连接（SDK 加载 + WebSocket 通信）

## 部署到 GitHub Pages

1. 在 GitHub 上创建仓库 `xmov-cultural-guardian`
2. 推送代码到仓库：
   ```bash
   git init
   git add .
   git commit -m "init: 文化遗产数字守护者 Demo"
   git remote add origin https://github.com/YeatsLiao/xmov-cultural-guardian.git
   git branch -M main
   git push -u origin main
   ```
3. 进入仓库 **Settings → Pages**
4. Source 选择 **Deploy from a branch**，分支选 `main`，目录选 `/ (root)`
5. 点击 **Save**，等待部署完成
6. 访问地址：`https://yeatsliao.github.io/xmov-cultural-guardian/`

> 主文件已命名为 `index.html`，GitHub Pages 根路径即可直接访问。
