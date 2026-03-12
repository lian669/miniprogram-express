# Copilot 使用说明（仓库指南，供 AI 代码代理参考）

本文件汇总在此微信小程序仓库中可直接观察到的架构、约定与常用开发流程；以便 AI 代理快速上手、给出准确修改建议或补全代码。

## 概览
- 项目类型：微信小程序（TypeScript + Less）。源码存放在 `miniprogram/`。
- 主要目录：`miniprogram/pages`（页面），`miniprogram/components`（组件），`miniprogram/utils`（工具函数），`typings/`（类型声明）。
- 入口：[miniprogram/app.ts](miniprogram/app.ts)。

## 关键实现模式（可直接从代码观察）
- 页面与组件使用微信小程序原生框架的 `Component()` 与 `App()` 函数（见 [miniprogram/pages/index/index.ts](miniprogram/pages/index/index.ts) 与 [miniprogram/components/navigation-bar/navigation-bar.ts](miniprogram/components/navigation-bar/navigation-bar.ts)）。
- 状态更新：通过 `this.setData({...})` 修改组件/页面数据。
- 组件约定：在导航组件中使用 `options.multipleSlots=true`，并通过 `properties`/`data`/`methods` 组织（参见导航组件文件）。
- 安全区与菜单按钮：导航组件使用 `wx.getMenuButtonBoundingClientRect()` 与 `wx.getSystemInfo()` 计算安全区与内边距，处理 iOS/Android/devtools 的差异。

## 类型与构建相关
- TypeScript 配置位于 [tsconfig.json](tsconfig.json)。
- 项目使用本地 `typings/` 目录作为类型根（`typeRoots` 指向 `./typings`），并在 `package.json` 中声明 `miniprogram-api-typings` 为开发依赖。
- 仓库没有定义 npm 脚本（`package.json` 的 `scripts` 为空）。常用检查命令（可由开发者/代理建议运行）：
  - `npx tsc -p tsconfig.json --noEmit`：快速做类型检查（不改变文件）。

## 运行与调试（项目特定）
- 主要运行/调试方式：使用微信开发者工具打开项目根目录（`f:\微信小程序`），因为没有前端打包流程，工具会直接读取 `miniprogram/` 下的源文件。
- 若需要本地类型检查或编辑器内报错修复，运行上面的 `tsc` 命令或在 IDE 中启用 TypeScript 项目支持。

## 可见的集成点与外部依赖
- `miniprogram/app.ts` 中有 `wx.login()` 调用并记录 `res.code`，表明存在后端服务用于换取 openId/session（实现不在仓库内，修改时请注意不破坏该流程）。
- 组件与页面通过 `wx.navigateTo` / `wx.navigateBack` 等 API 进行页面间导航，注意 `delta` 在导航组件中作为返回深度参数使用。

## 项目约定与风格（代码代理应遵守）
- 使用原生小程序 API（`wx.*`）而非第三方框架；修改时保持现有风格（`Component({ ... })` 与 `App({...})`）。
- 数据更新时间优先使用 `this.setData`；不要替换为 React/Vue 风格写法。
- 组件内尽量保留 `properties` / `data` / `methods` 的分区结构，保持现有属性命名（例如 `extClass`, `animated`, `show` 等）。

## 修改与 PR 建议（对 AI 代理的具体指示）
- 小修复（拼写、简单逻辑、格式化）可直接修改并提交 PR。提交时保持文件结构不变。
- 若要添加构建脚本或 CI（比如 `npm run build`），先在 PR 描述中说明新增脚本的目的，并保证不会影响在微信开发者工具中的直接加载行为。
- 涉及到后端交互（例如在 `app.ts` 使用 `wx.login` 的流程），请在 PR 中注明后端接口契约（参数/返回）或先与维护者确认接口细节。

## 快速定位示例
- 查看 App 启动逻辑：[miniprogram/app.ts](miniprogram/app.ts)
- 页面示例（数据/事件/用户信息处理）：[miniprogram/pages/index/index.ts](miniprogram/pages/index/index.ts)
- 导航栏组件（安全区、菜单按钮、自定义属性）：[miniprogram/components/navigation-bar/navigation-bar.ts](miniprogram/components/navigation-bar/navigation-bar.ts)
- 常用工具函数： [miniprogram/utils/util.ts](miniprogram/utils/util.ts)

---
如果这些内容有遗漏或你希望我补充更详细的部分（例如添加常用 `tsc` 命令示例、PR 模板或 CI 建议），告诉我需要补充的点，我会迭代更新。 
