# ElizaOS — Developer Quick Start

这是为希望在本地开发、构建和测试 ElizaOS 的开发者准备的快速入门指南，侧重于常见环境（macOS / Linux / Windows + WSL），并包含 bun、Node、turbo/lerna 的基本步骤与常见问题排查。

## 目标
- 快速在本地把 monorepo 克隆并运行起来
- 说明 Windows（原生 PowerShell）与 WSL 之间的注意事项
- 提供常见调试和 CI 本地化检查建议

---

## 要求
- Node.js v23.x（仓库 `package.json` 中声明为 `23.3.0`）
- bun v1.2.21（或兼容版本）
- git
- bash（Linux / macOS 默认；Windows 建议使用 WSL2 或 Git Bash / MSYS2）

> 注意：仓库中多个 scripts 使用 bash 脚本 (`scripts/*.sh`)。在 Windows 上强烈建议使用 WSL2 来获得一致体验。

---

## 1) 克隆仓库

```bash
git clone https://github.com/elizaos/eliza.git
cd eliza
```

如果仓库使用子模块（`scripts/init-submodules.sh`），postinstall 会尝试初始化它们。

---

## 2) 环境准备

### macOS / Linux
- 安装 Node.js v23.x
  - 使用 nvm: `nvm install 23.3.0 && nvm use 23.3.0`
- 安装 bun
  - 官方安装脚本（参看 https://bun.sh）

### Windows（推荐使用 WSL2）
- 安装 WSL2 并设置一个 Ubuntu 子系统
- 在 WSL 内按照 Linux 步骤安装 Node 和 bun

如果不使用 WSL，可使用 Git Bash 或 MSYS2，但可能需要额外兼容层以运行仓库内的 bash 脚本。

---

## 3) 安装依赖

仓库使用 bun 作为包管理器（`package.json` 指定 `bun@1.2.21`）。在项目根目录运行：

```bash
# 使用 bun 安装
bun install
```

如果你没有 bun，可临时使用 npm/yarn，但注意某些 bun 特性与命令（如 bunx）可能无法工作。

---

## 4) 初始化子模块（如需要）

安装后 `postinstall` 脚本会尝试运行 `bash ./scripts/init-submodules.sh`。
如果你的环境不支持 bash 或该脚本失败，请手动初始化：

```bash
git submodule update --init --recursive
```

---

## 5) 运行开发环境

常用命令（在项目根）：

- 启动 CLI（turbo 负责筛选子包运行）

```bash
bun run start
# 或
npm run start
```

- 仅启动 app 前端

```bash
bun run start:app
```

- 以开发模式监听更改（项目使用一个自定义脚本）

```bash
bun run dev
```

- 构建所有包（略过 app 和 config）

```bash
bun run build
```

- 运行测试

```bash
bun run test
```

备注：很多命令使用 `turbo run`，turbo 会并行和缓存构建/测试任务。

---

## 6) 快速运行示例（interactive）

项目包含 `examples/`，例如交互式演示：

```bash
cd examples
OPENAI_API_KEY=your_key bun run standalone-cli-chat.ts
```

---

## 7) Windows 特别注意事项与 PowerShell 兼容性

- `package.json` 的 `postinstall` 和许多脚本使用 bash；在纯 PowerShell 中这些脚本可能失败。建议：
  - 在 Windows 上使用 WSL2 并在 WSL 的 shell 内运行 `bun install` / `bun run` 等。
  - 或者为常用脚本添加 PowerShell 对等实现（例如 `scripts/init-submodules.ps1`），可以手动扩展。

- 如果你希望我为仓库添加 PowerShell 脚本替代项或在 `postinstall` 中实现平台检测并执行不同脚本，我可以提交一个小 PR 来实现。

---

## 8) 常见问题排查

- 子模块未初始化或缺失资源：
  - 运行 `git submodule update --init --recursive`
- bun 命令无法运行：
  - 确认 `bun` 已正确安装并在 PATH 中；在 WSL 下重新安装 bun
- Node 版本不匹配：
  - 使用 nvm 切换 Node 版本到 23.x
- Windows 上 shell 脚本报错：
  - 在 WSL 中执行；或我可帮你补充 PowerShell 版本的脚本

---

## 9) 本地 CI 快速检查建议

在本地运行这几个命令以模拟 CI 检查：

```bash
# 1) 格式检查
bun run format:check

# 2) 类型检查（如果你想我添加 script: typecheck 我可以做）
# 示范（可能需要在 package.json 中添加脚本）
# tsc -b --noEmit

# 3) 单元测试
bun run test
```

---

## 10) 我可以为你做的变更（可选）
- 添加 `DEVELOPER_QUICKSTART.md`（已创建）
- 在 `scripts/` 中添加 Windows PowerShell 版本的子模块初始化脚本，并更新 `package.json` 的 `postinstall` 做平台检测
- 为根 `package.json` 添加 `typecheck` 脚本并将其纳入 `lint` 或 CI 配置
- 扫描 `packages/` 并生成单页审计（依赖、测试覆盖、脚本）

如果你想继续其中一项，请告诉我要先做哪一个，我会把对应 todo 标记为进行中并开始实现。
