# TapTap 上架参考

H5 游戏上架 TapTap 的通道、流程、资质、物料、包体与工具。全部条目查证自 TapTap 官方文档与 npm 包源码，查证日期 2026-11-26，出处见文末。

## 通道：H5 小游戏

TapTap 小游戏支持两种包体（[小游戏介绍](https://developer.taptap.cn/minigameapidoc/quick-start/guide/minigames-intro/)）：

| 包体 | 形态 | 判定 |
|------|------|------|
| 普通小游戏 | `game.js` + `game.json` + `project.config.json` 三件套，运行时是没有 DOM 的 JS VM | 游戏是 DOM 渲染，走不通 |
| H5 小游戏 | 上传根目录含 `index.html` 的目录，平台负责打包 | **走这条**：`npm run build` 的 `dist/` 正是这个形态 |

商店 APK 是另一条路（[docs/store](https://developer.taptap.cn/docs/store/)）：必须支持 ARM64，包名唯一且被移动应用备案校验，本游戏不走。

两条硬约束都指向 H5：[MCP 安装指南](https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/mcp-setup/) 明示「当前 MCP 仅支持 H5 小游戏」；[广告接入指南](https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/ad-integration-guide/) 明示广告变现仅支持星火、H5 小游戏和 TapTap 制造的游戏。

变现零分成：[IAA 游戏](https://developer.taptap.cn/minigameapidoc/quick-start/monetization/IAA-game/) 原文「0 分成，开发者可得到 100% 的现金分成比例」，个人开发者可接。

## 上架流程

1. 注册并认证 TapTap 开发者 → 开发者中心「创建游戏」，游玩提供方式选「小游戏」
2. 商店资料完善信息并提交审核（首审官方预计 1–3 日，更新版本预计 1 日内）
3. 包体：商店 → 小游戏管理 → 包体管理 → 上传包体；版本号 `V x.y.z`，三段整数、逐版递增，不得与已上线或审核中的版本重复
4. 资质：商店 → 小游戏管理 → 小游戏资质（前置备案见下）；仅小游戏方式时防沉迷由平台默认托管
5. 提审：开发版本 →「提审」→ 商店资料补全 → 选「正式上线」→ 提交审核
6. 发布状态三选：敬请期待 / 预约 / 正式上线（试玩版）；正式上线要求包体与资质都通过

来源：[创建小游戏](https://developer.taptap.cn/minigameapidoc/quick-start/guide/creation-improvement/)、[版本提审](https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/version/)

## 资质：无内购无版号，前置备案是关键路径

- 不涉及内购无需版号；[小游戏审核规范](https://developer.taptap.cn/minigameapidoc/tap-operation/operation-standards/review-standards/) 1.1.1 原文「无版号游戏不得包含内购计费内容」
- 小游戏前置备案是一站式服务（整合原小游戏备案与 ICP 核准），三步：填写核准资料 → 电子化核验（负责人微信/支付宝扫码，约 10 分钟）→ 提交审核
- 核准资料含：开发者认证、小游戏包体、软件著作权人、游戏内容介绍说明、负责人基本情况、身份信息核验、补充材料
- 时长：全程预计 **20 个工作日**；初审 **12–16 个工作日**；初审过后工信部向负责人发短信，**24 小时内**不完成核验即退回重报
- 主体：个人认证时负责人必须是认证主体本人；企业认证可授权第三人
- 企业主体认证需提交加盖公章的《TapTap 认证申请公函》，模板存本目录 `TapTap认证申请公函（模板）-20240313.docx`

来源：[小游戏前置备案](https://developer.taptap.cn/minigameapidoc/quick-start/guide/pre-filing/)

## 物料

两处规格：小游戏提审按 [宣发物料指引](https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/material/)，创建页链接的 [游戏物料要求](https://developer.taptap.cn/docs/store/release/publish/material/)（v4）补图标与文案细则。

### 小游戏宣发物料

| 物料 | 规格 | 数量 |
|------|------|------|
| 简介 | — | 1 |
| 横版宣传图 | 1920×1080，≤2MB，JPG/PNG | 1 |
| 方形宣传图 | 1440×1440，≤2MB，JPG/PNG | 1 |
| 实机截图 | ≥1280×720，单张 ≤2MB | 至少 3 张 |
| 实机录屏 | 横 16:9 ≥1280×720 或竖 9:16 ≥720×1280，≤500MB，MP4，H.264/HEVC | 1 |
| 首页推荐素材 | 勾选 | — |

图片统一要求：带游戏名称或 LOGO，且不得出现名称、LOGO 以外的文字。

### 游戏物料要求（v4）要点

- 图标（必填）：≥512×512、1:1、png/jpg，直角方图勿裁圆角，不得用默认图标、白底、黑底、透明底，不得带「NEW」「HOT」类角标
- 文案（必填）：简介（建议前 50 字说清玩法）、更新日志；均不得含联系方式、兑换码、交流群与外部链接
- 实机截图：横 ≥1280×720 或竖 ≥720×1280，全组方向与比例一致，至少 3 张且不重复，单张 ≤4MB（建议 ≤1MB）
- 宣传图 16:9（必填）：≥1920×1080、≤4MB，须展示游戏名，除游戏名外无其他文本，不得放游戏图标，不得用未排版截图或拼贴
- 宣传图 1:1：≥1440×1440、≤4MB，主体与 16:9 一致（仅在上传竖版录屏时必填）
- 竖版封面 600×900、横版封面 460×215、游戏库壁纸 3840×1240 属「电脑/主机」侧物料，手机详情页不用

两页对同一物料的体积上限不一致（截图单张 2MB vs 4MB、录屏 500MB vs 5GB），按小游戏侧提审更稳。

审核雷区（小游戏审核规范 3.2 / 3.3 / 3.4）：截图不得出现手机 UI、水印、虚假下载按钮；宣传图不得多图拼接、不得出现游戏 ICON；标题不得含「极速版」「送百抽」类词与「最好玩」等广告法禁词。

## 包体

- H5：`prepare_h5_upload` 校验目录根有 `index.html`，`upload_h5_game` 用 zlib level 9 压缩后上传，`gamePath` 必须是相对路径（如 `dist`）
- 普通小游戏：首包 ≤60MB，且须正确接入 TapTap 登录
- 我们的包：`npm run build` 产出 `dist/`（`index.html` + `assets/`），与 H5 要求同形，无需改造

## 工具：TapTap MCP

`@taptap/instant-games-open-mcp`，v1.24.14，npm 最后更新 2026-09-09；以下均核对包源码：

- 认证：OAuth 2.0 设备码流，`start_oauth_authorization` 返回二维码 URL，TapTap App 扫码后 `complete_oauth_authorization` 轮询落 token
- H5 上传两步：`prepare_h5_upload`（选应用 + 校验 `index.html`）→ `upload_h5_game`（**横竖屏必须问用户**：1 竖屏 / 2 横屏，工具描述明令不得自行猜测）
- 素材与资料：`upload_image`（单张 ≤4MB，JPG/PNG/GIF/WebP）、`update_app_info`（icon ≥512×512、banner ≤4MB 且 ≥1920×1080、截图至多 4 张）
- 查状态：`get_app_status`（0 未发布 / 1 审核中 / 2 审核失败 / 3 已上线）
- 另有 `create_app`、`list_developers_and_apps`、`select_app`，以及排行榜、云存档、分享、联机、广告接入等成套工具

## 出处

- 小游戏文档指引 <https://developer.taptap.cn/minigameapidoc/quick-start/document-guide/>
- 小游戏介绍 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/minigames-intro/>
- 创建小游戏 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/creation-improvement/>
- 小游戏前置备案 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/pre-filing/>
- 宣发物料指引 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/material/>
- 版本提审 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/version/>
- 小游戏审核规范 <https://developer.taptap.cn/minigameapidoc/tap-operation/operation-standards/review-standards/>
- MCP 安装指南 <https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/mcp-setup/>
- 广告接入指南 <https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/ad-integration-guide/>
- IAA 游戏 <https://developer.taptap.cn/minigameapidoc/quick-start/monetization/IAA-game/>
- 游戏物料要求（v4）<https://developer.taptap.cn/docs/store/release/publish/material/>
- npm 包 `@taptap/instant-games-open-mcp` <https://www.npmjs.com/package/@taptap/instant-games-open-mcp>
