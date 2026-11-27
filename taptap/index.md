# TapTap 上架参考

H5 游戏上架 TapTap 的通道、流程、资质、物料、包体与工具。全部条目查证自 TapTap 官方文档、npm 包源码与 CLI 实测，查证日期 2026-11-26（2026-11-27 增补官方 CLI 发布通道、备案时点、预检实况、隐私办理路径与动态必填），出处见文末。

## 通道：H5 小游戏

TapTap 小游戏支持两种包体（[小游戏介绍](https://developer.taptap.cn/minigameapidoc/quick-start/guide/minigames-intro/)）：普通小游戏与 H5 小游戏。两者功能、曝光无差别，差别在运行环境与交付形态——选哪条看游戏本身是不是网页。

### 运行环境

- **普通小游戏**：[开发教程总览](https://developer.taptap.cn/minigameapidoc/dev/tutorial/overview/) 原文——「小游戏的运行环境是一个绑定了一些方法的 JavaScript VM。不同于浏览器，这个运行环境没有 BOM 和 DOM API，只有 tap 系列 API」；同页「没有 BOM API，因此也就没有 window 对象」，从 H5 迁入要自己实现 window 兼容，Cocos/Laya/Egret/Unity 须靠 Adapter 在引擎层抹平 BOM/DOM。项目结构 `game.js`（入口）+ `game.json`（配置：横竖屏、分包），画图 `tap.createCanvas()`、触摸 `tap.onTouchStart()`。
- **H5 小游戏**：普通网页，跑在 WebView 里，`window`、`document`、localStorage、`<audio>` 齐全。

### 交付两步

- **普通小游戏**：① 按三件套写游戏，首包 ≤60MB；② 用 TapTap 打包工具打成指定 zip 上传，且包体要求已接入 TapTap 登录。
- **H5 小游戏**：① `npm run build` 出 `dist/`；② 打 zip 交官方 CLI，平台自己打包。MCP 上传通道已废弃——2026-11-27 实测 `upload_h5_game` 返回「H5 游戏发布已迁移至官方 TapTap CLI，请停止重试当前 MCP 上传/发布流程」。现行命令 `taptap-cli upload-h5-package <zip>`：zip **根下只能有一个游戏文件夹**、`index.html` 在该文件夹第一层（与 MCP 时代的平铺要求相反），先 `--dry-run` 看 `archive_preflight.violations` 为空再 `--yes`；`--screen-orientation` 0=横屏 1=竖屏（与 MCP 的 1竖/2横 相反）。上传成功 ≠ 绑定，还要 `app select-package` 把包绑上主包位。

### 对 qtgame-tycoon 的走查

游戏是 `innerHTML` 渲染 DOM（14 处）、localStorage 存档、`new Audio` 播 BGM：放进普通小游戏，开局渲染就崩（那个环境没有 `document`），要上就得把 640 行 CSS（19 个 keyframes、20 处绝对定位）搬进 canvas + `tap.*`，等于重写；放进 H5，`dist/` 原样上传，一行不改。

### 其余差别

- **工具与广告**：[MCP 安装指南](https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/mcp-setup/) 明示「当前 MCP 仅支持 H5 小游戏」；[广告接入指南](https://developer.taptap.cn/minigameapidoc/quick-start/mcp-guide/ad-integration-guide/) 明示广告变现仅支持星火、H5 小游戏和 TapTap 制造的游戏——H5 走开发者中心「小游戏广告」（开通 5 分钟、AI 写代码），普通小游戏走 Dirichlet 广告联盟，两边都是 [0 分成、开发者拿 100%](https://developer.taptap.cn/minigameapidoc/quick-start/monetization/IAA-game/)（原文「0 分成，开发者可得到 100% 的现金分成比例」），个人开发者可接
- **曝光**：文档未按包体区分流量资源，即点即玩专区、首页推荐两者同享
- **TapTap 登录**：硬要求只写在普通小游戏的包体格式里，H5 侧无明文，提审时留意

商店 APK 是另一条路（[docs/store](https://developer.taptap.cn/docs/store/)）：必须支持 ARM64，包名唯一且被移动应用备案校验，本游戏不走。

## 上架流程

1. 注册并认证 TapTap 开发者 → 开发者中心「创建游戏」，游玩提供方式选「小游戏」
2. 商店资料完善信息并提交审核（首审官方预计 1–3 日，更新版本预计 1 日内）
3. 包体：商店 → 小游戏管理 → 包体管理 → 上传包体；版本号[文档](https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/version/)写 `V x.y.z` 三段整数、逐版递增，实测 H5 版本是平台生成的 `V-YYYYMMDD`（如 V-20261127），CLI 提审不传版本号；两口径都要求不与已上线或审核中的版本重复
4. 资质：商店 → 小游戏管理 → 小游戏资质（前置备案见下）；仅小游戏方式时防沉迷由平台默认托管
5. 提审：开发版本 →「提审」→ 商店资料补全 → 选「正式上线」→ 提交审核
6. 发布状态三选：敬请期待 / 预约 / 正式上线（试玩版）；正式上线要求包体与资质都通过。落在字段上是 `region_flag_android/ios`，H5 可选 android `[1,3,4]`、iOS `[1,3,0]`，「提供游玩」= android 4 / iOS 0；**置为提供游玩会触发动态必填（见下「物料」）**

来源：[创建小游戏](https://developer.taptap.cn/minigameapidoc/quick-start/guide/creation-improvement/)、[版本提审](https://developer.taptap.cn/minigameapidoc/quick-start/guide/game-publish/version/)

## 开发者入驻

- 注册开发者：无需审核，但最多建 2 个「未发布」游戏且不能提审——要上线必须转认证开发者
- 认证开发者：个人（厂商名称 + 身份证）或企业（营业执照 + 认证公函），提交后官方审核 2 个工作日
- 认证公函用 TapTap 官方模板（本目录那份）：加盖真实公章（**不支持电子公章**）、运营人签字与日期缺一不可、日期填当天；**有效期一年，每年更新**

来源：[开发者注册与登录](https://developer.taptap.cn/minigameapidoc/quick-start/guide/registration-login/)

## 资质：无内购无版号，前置备案是关键路径

- 不涉及内购无需版号；[小游戏审核规范](https://developer.taptap.cn/minigameapidoc/tap-operation/operation-standards/review-standards/) 1.1.1 原文「无版号游戏不得包含内购计费内容」
- 备案与内购无关，管的是「游戏在公网上给人玩」：[小游戏文档指引](https://developer.taptap.cn/minigameapidoc/quick-start/document-guide/) 原文「小游戏正式上架平台必须前置完成小游戏前置备案流程」，[小游戏前置备案](https://developer.taptap.cn/minigameapidoc/quick-start/guide/pre-filing/) 原文「小游戏在上线前需完成该流程」——**最晚上线前办完**，倒排按下面的 20 个工作日
- 实测（2026-11-27，qtgame-tycoon / app 1104157）：非关卡 H5 应用的资质清单把 ICP 备案列为**非必需**，备案进度查询无记录，`app precheck-review` 零 blocker 且警告里没有备案——平台当前没卡这个应用的备案；官方「上架前必须」口径不变，预检把备案列进 blocker 时再启动办理
- 小游戏前置备案是一站式服务（整合原小游戏备案与 ICP 核准），三步：填写核准资料 → 电子化核验（负责人微信/支付宝扫码，约 10 分钟）→ 提交审核
- 核准资料含：开发者认证、小游戏包体、软件著作权人、游戏内容介绍说明、负责人基本情况、身份信息核验、补充材料
- 时长：全程预计 **20 个工作日**；初审 **12–16 个工作日**；初审过后工信部向负责人发短信，**24 小时内**不完成核验即退回重报
- 主体：个人认证时负责人必须是认证主体本人；企业认证可授权第三人
- 隐私合规属独立材料域（小游戏域），应用资质接口不支持直写：声明在开发者中心网页「小游戏隐私合规」面板保存——平台统一托管，一道判断题「是否收集用户隐私」，游戏不采集任何数据就选「否」；CLI 无保存命令，保存后 `qualification prepare-mini-app-privacy`（version_id 用 H5 包的 package_id）出扫描报告 → `submit-mini-app-privacy` 提交独立审核
- 适龄（8/12/16）与 AI 声明同样是独立审核：save → submit → `approved` 才生效，提交是 high-risk-write、须用户确认
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

动态必填（2026-11-27 实测，字段随状态就地变必填）：分发状态置「提供游玩」→ 实机录屏 `gameplay_demo_video` 必填；上传竖版录屏 → 1:1 宣传图 `square_promo_image` 必填。`app precheck-review` 会把它们列成 MISSING_REQUIRED，按序补即可。

### 游戏物料要求（v4）要点

- 图标（必填）：≥512×512、1:1、png/jpg，直角方图勿裁圆角，不得用默认图标、白底、黑底、透明底，不得带「NEW」「HOT」类角标
- 文案（必填）：简介（建议前 50 字说清玩法）、更新日志；均不得含联系方式、兑换码、交流群与外部链接
- 实机截图：横 ≥1280×720 或竖 ≥720×1280，全组方向与比例一致，至少 3 张且不重复，单张 ≤4MB（建议 ≤1MB）
- 宣传图 16:9（必填）：≥1920×1080、≤4MB，须展示游戏名，除游戏名外无其他文本，不得放游戏图标，不得用未排版截图或拼贴
- 宣传图 1:1：≥1440×1440、≤4MB，主体与 16:9 一致（仅在上传竖版录屏时必填）
- 竖版封面 600×900、横版封面 460×215、游戏库壁纸 3840×1240 属「电脑/主机」侧物料，手机详情页不用

两页对同一物料的体积上限不一致（截图单张 2MB vs 4MB、录屏 500MB vs 5GB），按小游戏侧提审更稳。

审核雷区（小游戏审核规范 3.2 / 3.3 / 3.4）：截图不得出现手机 UI、水印、虚假下载按钮；宣传图不得多图拼接、不得出现游戏 ICON；标题不得含「极速版」「送百抽」类词与「最好玩」等广告法禁词。

## 工具：发布走官方 CLI，MCP 只剩查询

**官方 CLI（2026-11-27 实测，版本 2026.11.26）**：`npm i -g @taptap/cli`，命令 `taptap-cli`。要点：

- 登录：`taptap-cli auth login --no-wait --json` 拿 `verification_url` 给用户扫码，用返回的 `device_code`/`expires_at` 持续轮询；token 存本机 `~/.taptap-cli/taptap.json`
- Skill 内嵌在二进制里：`taptap-cli skills list`、`skills read taptap-package-management`——不必往仓库装任何文件
- 应用创建时没选包体类型，上传 H5 包报 422「该应用类型不支持上传 H5 包体…创建时未选择类型」：先 `qualification save-qualification-draft` 存 `app_features.package_types:["h5"]` 再传
- 资料写入 read-before-write：`app get-module` 取 `current_value` 当 `expected`，`app save-changes` 先 `--dry-run` 再 `--yes`；图片值必须 `{url, info}` 且 info 五项（size/format/width/height/image_ave.rgb）齐全，缺一项 400；首页推荐语 ≤25 字
- 资质命令族：`qualification save-qualification-draft`（类型声明、app_features 事实、AI 声明最小输入 `provide_aigc_service=false`+`package_types`）、`save-age-grade`/`submit-age-grade`（8/12/16，提交是 high-risk-write，须用户确认）、H5 备案 `save-h5-game-filing`/`submit-h5-game-filing`、小游戏隐私 `prepare-mini-app-privacy`/`submit-mini-app-privacy`
- 提审：`app precheck-review`（预检）→ `app submit-review`；预检返回的 blockers/warnings 就是真实卡点，别拿文档猜
- 自测二维码（提审包与自测包分开）：`taptap-cli test-qr-code --app-id <id> --dev-id <id> --data '{"package_id":<包id>}' --output <png>`

**MCP `@taptap/instant-games-open-mcp`（v1.24.15，上传/发布已废弃）**：认证（OAuth 2.0 设备码流，扫码落 token）、素材与资料（`upload_image`、`update_app_info`）、查状态（`get_app_status`：0 未发布 / 1 审核中 / 2 审核失败 / 3 已上线）、`create_app`/`list_developers_and_apps`/`select_app`、排行榜/云存档/分享/联机/广告接入仍可用；**`prepare_h5_upload`→`upload_h5_game` 已废弃，见上「交付两步」**。

## 出处

- 小游戏文档指引 <https://developer.taptap.cn/minigameapidoc/quick-start/document-guide/>
- 开发教程总览（运行环境与 Tap API）<https://developer.taptap.cn/minigameapidoc/dev/tutorial/overview/>
- 开发者注册与登录 <https://developer.taptap.cn/minigameapidoc/quick-start/guide/registration-login/>
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
- TapTap CLI 文档 <https://developer.taptap.cn/v3/cli/>
- npm 包 `@taptap/cli` <https://www.npmjs.com/package/@taptap/cli>
