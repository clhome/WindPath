# WindPath 项目重命名与迁移任务清单

> 依据《QuickPath 更名为 WindPath（御风路径）标准化迁移手册》，将 QuickPath 完整迁移更名为 WindPath。

## 任务执行进度

- [x] 1. 基础配置与清单更新
  - [x] 1.1 更新 `Cargo.toml`（包名 `windpath`，主页与仓库地址更新）
  - [x] 1.2 更新 `build.rs`（文件描述、程序集名称 `WindPath.exe`，元数据及注释）
  - [x] 1.3 更新 `app.manifest`（AssemblyIdentity name 为 `WindPath`）
- [x] 2. 底层系统标识与通信管道替换
  - [x] 2.1 替换单实例互斥量 Mutex 名称为 `Local\WindPath_SingleInstance_Mutex`
  - [x] 2.2 替换主窗口、托盘、设置中心、悬浮条、任务栏监控等所有 Win32 窗口类名与标题
  - [x] 2.3 统一替换内部 Windows 自定义消息宏前缀 (`WM_WINDPATH_*`)
  - [x] 2.4 替换各后台 Worker 线程名称及调试日志文件名 (`windpath_debug.log`)
  - [x] 2.5 替换剪贴板探测类名与测试用专属格式标识
- [x] 3. 老用户数据静默迁移与开机自启动清理
  - [x] 3.1 编写配置静默迁移逻辑（从 `%APPDATA%\QuickPath` 迁移到 `%APPDATA%\WindPath`，兼容便携版 `quickpath.json` -> `windpath.json`）
  - [x] 3.2 改造 `autostart.rs`：开机自启任务与注册表项更名为 `WindPath`，启动时主动检测并清除旧版 `QuickPath` 残留自启项
- [x] 4. 业务逻辑、多语言与 UI 文案替换
  - [x] 4.1 更新 `locales/zh-CN.toml` 与 `locales/en-US.toml` 语言包
  - [x] 4.2 更新 `src/ui/settings.rs`、`src/ui/tray.rs`、`src/ui/floating_bar.rs`、`src/monitor/` 中的品牌展示文案与 GitHub 链接
  - [x] 4.3 更新 `src/rules/version.rs`、`src/rules/i18n.rs`、`src/win32/updater.rs` 中的版本检查逻辑与测试用例
  - [x] 4.4 检查更新 `src/dialog/`、`src/managers/`、`src/tab/`、`src/win32/` 中的相关引用
- [x] 5. 打包构建与 CI/CD 脚本迁移
  - [x] 5.1 重命名并更新 WiX MSI 脚本（`quickpath.wxs` -> `windpath.wxs`，更新 `build-msi.ps1`）
  - [x] 5.2 更新 MSIX 配置（`AppxManifest.xml` 及 `build-msix.ps1`）
  - [x] 5.3 更新 GitHub Actions 流水线（`.github/workflows/release.yml` 与 `ci.yml`）
- [x] 6. 门面文档与品牌过渡说明
  - [x] 6.1 更新 `README.md`、`README_CN.md`、`Document/README.md`，添加“WindPath（原 QuickPath）”品牌过渡说明
- [x] 7. 全量编译验证与清理
  - [x] 7.1 运行全局残留扫描，确保无未处理的旧名称
  - [x] 7.2 运行 `cargo check` 和 `cargo test` 确保所有 254+ 单元测试 100% 通过
  - [x] 7.3 清理临时生成的探测排查文件
- [x] 8. 用户详细使用说明书更新 (User Manual)
  - [x] 8.1 更新 `Document/User_Manual_CN.md` 为 WindPath（御风路径）并添加品牌过渡说明与对应配图
  - [x] 8.2 更新 `Document/User_Manual_EN.md` 为 WindPath 并同步更新所有安装、配置与 FAQ 说明与对应英文配图
- [x] 9. 硬件看板网速展示优化
  - [x] 9.1 修改 `src/monitor/window/tooltip.rs`，移除看板网速中的 MB/s 显示
  - [x] 9.2 更新单元测试断言
  - [x] 9.3 运行全量测试验证
- [x] 10. 硬件看板内存展示精简
  - [x] 10.1 更新语言包 `locales/zh-CN.toml` 中的 `tooltip_memory` 为 `"内存"`
  - [x] 10.2 修改 `src/monitor/window/tooltip.rs` 中的内存格式化逻辑，精简为 `内存: {percent}% ({used} GB / {total} GB)`
  - [x] 10.3 同步更新 `src/monitor/window/tooltip.rs` 的单元测试断言
  - [x] 10.4 运行 `cargo test` 验证修改并确保通过
- [x] 11. 移除设置中心副标题文案与渲染
  - [x] 11.1 从 `locales/zh-CN.toml` 与 `locales/en-US.toml` 中移除 `subtitle`
  - [x] 11.2 从 `src/ui/settings.rs` 中移除设置中心副标题的绘制逻辑，并优化主标题上下居中对齐
  - [x] 11.3 运行全量单元测试与编译检查验证
- [x] 12. 落地方案1：编辑器保存自动格式化与代码库格式修正
  - [x] 12.1 创建 `.vscode/settings.json` 配置 `editor.formatOnSave` 与 `rust-analyzer` 格式化器
  - [x] 12.2 执行 `cargo fmt --all` 修正现有未格式化文件
  - [x] 12.3 执行 `cargo fmt --all -- --check` 验证门禁 100% 通过
- [x] 13. 落地方案2：配置 Git pre-commit Hook
  - [x] 13.1 创建 `.githooks/pre-commit` 自动格式化脚本（仅针对已暂存的 Rust 文件）
  - [x] 13.2 配置本地 Git 指向 `core.hooksPath = .githooks`
  - [x] 13.3 验证 Hook 触发逻辑与自动格式化效果
- [x] 12. 点击日志路径支持直接打开查看
  - [x] 12.1 在 `src/dialog/detector.rs` 中实现 `open_debug_log()`，处理文件预检/自动创建及打开与回退逻辑
  - [x] 12.2 在 `src/ui/settings.rs` 的点击事件中对接 `HOVER_ID_LOG_CARD` 处理
  - [x] 12.3 优化 `render_log_card` 中日志路径的悬停高亮反馈（Fluent Accent 亮天蓝）
  - [x] 12.4 编写针对 `open_debug_log` 及日志交互的相关单元测试
  - [x] 12.5 运行全量单元测试与 `cargo check` 验证
- [x] 13. 删除中文对应版本信息中的“(原生极速 · 极简轻量)”
  - [x] 13.1 更新 `locales/zh-CN.toml`，将版本文案调整为 `"v{version} 正式版"`
  - [x] 13.2 更新 `src/ui/settings.rs` 中的 fallback 缺省版本文本为 `"v{} 正式版"`
  - [x] 13.3 运行单元测试与编译检查验证
- [x] 14. 自动化版本更新与 Git Tag 发布工具
  - [x] 14.1 设计并编写 Python 自动化发布脚本 `release.py`（版本校验、Cargo.toml 精准替换、Cargo.lock 同步、Git 提交与打标签推送）
  - [x] 14.2 编写根目录启动批处理 `release.bat`（UTF-8 编码、Python 探测、传参透传与交互适配）
  - [x] 14.3 在 `test/` 目录下编写自动化验证脚本，验证版本号提取、替换及防误伤逻辑
  - [x] 14.4 运行验证测试，确保跨平台与 Windows 终端体验顺畅，清理测试脚本
- [x] 15. 修复 WiX MSI 打包代码页编码问题 (LGHT0311)
  - [x] 15.1 更新 `packaging/msi/windpath.wxs`：在 `<Product>` 添加 `Codepage="936"`，在 `<Package>` 添加 `SummaryCodepage="936"` 适配中文环境
  - [x] 15.2 更新 `packaging/msi/build-msi.ps1`：优化 WiX v3 临时编译对象前缀为 `windpath_wix_*`
  - [x] 15.3 检查确保修改文件保持 UTF-8 无 BOM 与 LF 换行规范
- [x] 16. 优化路径跳转悬浮窗快捷键提示与显隐交互
  - [x] 16.1 更新语言包 `locales/zh-CN.toml` 与 `locales/en-US.toml`，引入 `{hotkey}` 动态快捷键占位符并精修专业文案
  - [x] 16.2 扩展 hotkey 模块，提供紧凑型规范化快捷键展示 `format_hotkey_compact`
  - [x] 16.3 改造 `I18n::floating_title` 支持动态快捷键参数替换
  - [x] 16.4 改造 `FloatingBarState` 与 `FloatingBar`，支持保存并动态刷新 hotkey，支持可见性检查
  - [x] 16.5 优化 `on_hotkey_triggered` 支持快捷键显示/隐藏切换（Toggle）
  - [x] 16.6 在 `update_global_config` 中联动更新悬浮条快捷键与即时重绘
  - [x] 16.7 编写单元测试验证动态快捷键替换与多语言呈现，运行全量测试验证
- [x] 17. 修复微软商店 (MSIX) 版本开机静默自启失效问题
  - [x] 17.1 在 `Cargo.toml` 为 `windows` crate 添加 `"ApplicationModel"` 与 `"Foundation"` 特性支持
  - [x] 17.2 在 `src/win32/autostart.rs` 中实现基于 WinRT `StartupTask` API 的包身份自启动管理与查询
  - [x] 17.3 在 `set_autostart` 与 `is_autostart_enabled` 中通过 `is_packaged()` 实现自动分流与状态同步
  - [x] 17.4 编写针对不同环境分流与自启状态处理的单元测试
  - [x] 17.5 运行 `cargo check` 与 `cargo test` 进行全量编译和回归验证





