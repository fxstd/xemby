# XEmby

基于 Flutter 构建的 Emby 客户端，目标是提供一个统一代码库下的多平台媒体浏览与播放体验。当前仓库已经完成了认证、媒体库浏览、搜索、详情页、继续观看，以及以 Windows 为主的原生播放链路。

## 项目简介

XEmby 面向 Emby 用户，聚焦以下几个核心场景：

- 连接并校验 Emby 服务器地址
- 登录账号并持久化会话
- 浏览媒体库、查看详情、搜索内容
- 回到上次播放位置继续观看
- 在播放器内完成基础播放控制、音轨/字幕切换与进度同步

当前代码库已经包含 `android/`、`ios/`、`windows/`、`macos/`、`linux/`、`web/` 多平台工程目录，但播放器主链路目前明确落在 Windows 原生 `libmpv` 方案上，其它平台仍以工程骨架和通用页面能力为主。

## 当前已实现

### 认证与会话

- Emby 服务器地址输入、连通性校验与服务器名识别
- 用户登录、访问令牌保存、用户 ID 持久化
- 启动后自动恢复会话
- 本地持久化设备 ID，保证 Emby 客户端身份稳定

### 媒体浏览

- 首页媒体库入口
- “继续观看”列表
- 各媒体库“最新添加”内容展示
- 媒体库分页加载
- 列表排序（名称、添加时间、年份、评分等）
- 全局搜索电影/剧集

### 详情页能力

- 媒体基础信息展示
- 剧集列表与 Next Up 能力
- 进入播放前选择音轨与字幕流
- 返回页面后自动刷新继续观看和最新内容

### 播放能力

- 通过 Emby `PlaybackInfo` 获取播放地址与媒体流信息
- 播放 / 暂停、拖动、音量、静音、倍速控制
- 音轨切换、字幕切换
- 字幕缩放与底部偏移设置，并持久化到本地
- 片头结束 / 片尾跳过配置，并持久化到本地
- 向 Emby 上报播放开始、进度、停止事件
- 播放结束后返回页面并刷新相关数据

### Windows 桌面体验

- 使用 FFI 直接接入原生 `libmpv`
- 自定义桌面窗口标题栏
- `libmpv-2.dll` 随 Windows 构建产物一并复制
- 为播放器准备了外窗 / 覆盖层模式相关控制逻辑

## 技术栈

- `Flutter` / `Dart`
- `flutter_riverpod`：状态管理
- `go_router`：路由管理
- `dio`：网络请求
- `Hive` + `SharedPreferences`：本地存储
- `cached_network_image`：海报 / 头像缓存
- `window_manager`：桌面窗口控制
- `ffi`：Windows 原生 `libmpv` 播放接入

## 项目结构

```text
lib/
  app/                    应用入口、路由、主题
  core/                   网络、存储、异常、工具
  features/
    auth/                 服务器配置、登录、会话恢复
    library/              首页、媒体库、详情、搜索
    playback/             播放信息、播放器状态与控制
  shared/                 通用组件
test/
  auth/                   认证相关单测
  core/                   通用能力单测
  playback/               字幕 / 跳过配置单测
tool/
  generate_xemby_icons.py 图标生成脚本
```

整体代码按 feature-first 组织，并在功能模块中使用 `presentation / domain / data` 的分层方式，便于后续继续扩展播放器平台适配或新增业务能力。

## 快速开始

### 环境要求

- 安装可兼容 `pubspec.yaml` 中 `sdk: ^3.11.0` 的 Flutter / Dart 环境
- 准备一个可访问的 Emby 服务端
- 如果在 Android 真机或模拟器上调试局域网 HTTP 服务，项目已开启明文流量访问

### 安装依赖

```bash
flutter pub get
```

### 启动项目

```bash
flutter run -d windows
```

也可以根据本机环境替换为其它 Flutter 设备，例如：

```bash
flutter run -d android
flutter run -d chrome
```

### 首次使用流程

1. 启动应用
2. 输入 Emby 服务器地址并完成校验
3. 登录 Emby 账号
4. 进入首页浏览媒体库、继续观看或搜索内容
5. 在详情页进入播放器

## 常用开发命令

```bash
flutter analyze
flutter test
```

如果需要重新生成品牌图标：

```bash
python tool/generate_xemby_icons.py
```

## 当前限制

- 播放器核心链路当前仅明确支持 Windows 原生 `libmpv` 外窗播放
- 其它平台虽然已具备 Flutter 工程结构，但播放体验仍需继续适配与验证
- 当前测试主要覆盖核心实体、配置解析和部分业务逻辑，尚未形成完整端到端验证链路

## 适合继续演进的方向

- 完善 Android / iOS / macOS / Linux 的播放器实现
- 增强播放内核抽象，降低平台差异对页面层的影响
- 补充更多 Widget / 集成测试
- 增加打包、分发与 CI 流程

## 说明

这个仓库已经不是 Flutter 默认模板项目，而是一个围绕 Emby 场景持续演进中的客户端工程。如果你想快速了解代码入口，建议优先查看：

- `lib/main.dart`
- `lib/app/router.dart`
- `lib/features/auth/`
- `lib/features/library/`
- `lib/features/playback/`
