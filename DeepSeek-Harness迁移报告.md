# DeepSeek Harness 全局迁移报告

生成时间：2026-08-14（Asia/Shanghai）  
Owner 线程：`1-4 DeepSeek Harness全局迁移 Owner`  
thread_id：`019ffdfa-4c38-7c31-8800-761dcf4f264a`  
回报对象：`0-12 PMO总控 | eli`（`019fe926-8944-7d90-8443-e6a768b0f024`）  
实际 model / effort：`UNKNOWN / UNKNOWN`（当前运行态不可二读，未假装确认）

## 结论

状态：**安装完成待启用，待 PMO 验收**。

官方 DeepSeek Harness CLI 已按固定版本 `0.1.0-rc.6` 隔离安装；透明入口、lock、供应链 integrity、CLI、配置合成、macOS Seatbelt 只读沙箱及 `approval: ask` 的 fail-closed 行为均已验证。全局 AGENTS 已完成最小迁移，Reasonix 仅保留“已退役、二进制只为回滚”的声明，未删除其二进制或 `~/.reasonix`。

隔离 headless 已到达模型选择层，并按预期以 `MISSING_CREDENTIAL` 退出。没有使用或读取现有凭证，也没有完成真实 DeepSeek 模型响应闭环，因此不得启用自动 DSH 路由，不得声称模型已可用。

## 权威上游与供应链

- 唯一仓库：[deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness)，公开、未归档、MIT。
- 唯一 npm 包：`@deepseek-ai/dsh@0.1.0-rc.6`。
- npm repository：`git+https://github.com/deepseek-ai/deepseek-harness.git`，目录 `apps/cli`。
- npm tarball：`https://registry.npmjs.org/@deepseek-ai/dsh/-/dsh-0.1.0-rc.6.tgz`。
- registry 与 lock 的 `dist.integrity` 一致：`sha512-brpZfED7ieRa2PQ5tUxMhHrM1pb2CmKFVM/f6yMULBDMicahk+Z2OsHgTwTDnoiZm23Ftu9rQz0NN4pflaoJcg==`。
- 上游明确标注 Developer Preview，未来可能出现破坏性变更；禁止静默升级。

## Preflight 与安装

| 项目 | 证据 |
|---|---|
| 开工 cwd | `/Users/USER/Documents/Codex/2026-08-14/deepseek-harness-2026-08-14` |
| Node / npm | `v22.16.0` / `10.9.2` |
| 开工时 dsh | 不存在，无覆盖 |
| Reasonix | `/Users/USER/.local/bin/reasonix`，`v1.22.0`；未删除、未修改 |
| 固定安装根 | `/Users/USER/.local/share/deepseek-harness` |
| 透明入口 | `/Users/USER/.local/bin/dsh` → 固定安装的 `node_modules/@deepseek-ai/dsh/lib/bin.js` |
| 固定版本 | `0.1.0-rc.6` |
| package.json SHA-256 | `543d101c6a31cbba2ab26975c99a02e8f6281ec57ad64fbfb88bbd9077b2c370` |
| package-lock.json SHA-256 | `59ca78fc107c7f71edc621caaba097a7ded83d7e9c25dc08225f8e4f08639d37` |
| 安装大小 | 约 `352804 KiB` |
| 最终磁盘 | 可用 `13028216 KiB`，占用 `94%` |

安装未使用 sudo、全局 npm install、pnpm 或系统 Node/npm 变更；使用 npm lock 与完整性校验固定依赖图，未启动 Web UI 或长期服务。

## CLI、headless、sandbox 与 approval 验证

- `dsh --version`：`0.1.0-rc.6`。
- `dsh --help`：确认正式一次性入口为 `dsh --profile headless "任务"`，并存在 `--dump-config` / `--dump-default-config`。
- 在 `mktemp` 隔离的 DSH_HOME 与 workspace 中完成 default/composed config dump；调用 cwd 即默认 workspace。
- 隔离 headless：2 秒退出，exit code `1`，诊断为 `MISSING_CREDENTIAL`、provider `deepseek-official`；没有 `SANDBOX_UNAVAILABLE`，证明安装、profile 启动和模型选择链已到达凭证层，但不证明模型可用。
- 官方默认组合原为 `workspace-write + ask`。隔离验证最小 home patch 后，composed config 明确为 `sandbox-policy.mode: read-only` 与 `approval.policy: ask`。
- macOS 实际沙箱探针：平台 `darwin`，runner `sandbox-exec`（Seatbelt），enforcement `full`；只读命令 exit `0`；写 marker exit `1`、命中 `operation not permitted`，marker 不存在。
- 实际 approval 服务探针：配置 `ask`，无 answerer 时 outcome 为 `unavailable`，生成 `approval/asked` 与 `approval/decided` 审计对，`granted=false`。
- 官方 DSH_HOME 已存在；仅新增 `/Users/USER/.dsh/cordis.patch.yml`。未读取、复制或输出 `.credentials.yaml` 内容；只核其权限为 `0600`。安全基线 SHA-256：`050c2dc5cb665c51e8961565bbe419b94ba1cc4d1371a7a268a8c4ed0f73bbec`。
- 两个本任务 `mktemp` 目录已精确清理；未清理系统 cache 或其他项目数据。

## AGENTS 迁移

- 备份：`/Users/USER/.codex/backups/deepseek-harness-migration-20260814-095507/AGENTS.md.before`
- 修改前 SHA-256：`[REDACTED_PHONE]b0b04728103be53b50cc62ba0811acf04219e3ce62b3642b6a820`
- 备份 SHA-256：与修改前一致。
- 修改后 SHA-256：`5f54f064f023d03239fcd49b79a06919a23498eb577aadda0705653b0ddf5da3`
- 修改后大小：`21710` bytes，低于 `32 KiB`。
- 最小变更：治理总则中的误派对象、第三节标题及旧 Reasonix 辅助执行段落；其他模型路由、网站、磁盘、项目业务段落未改。
- 残余 `Reasonix` 引用仅 1 处：退役/回滚声明。旧 `reasonix run`、JSON 参数与能力陈述为 0 处。
- 旧会话不会自动重载；仅新会话或重启后读取新规则。

## 第一性原理与红队结论

前提：辅助执行层成立的必要条件是可信供应链、固定依赖、可回滚入口、精确 cwd、可证文件隔离、fail-closed 审批与真实模型闭环，而不是“CLI 能启动”。

攻击/误用方式：同名第三方包、漂移依赖、覆盖既有入口、AGENTS 并发改写、把默认 `workspace-write` 误报成只读、把 headless 启动误报成模型可用、无交互 approval 静默放行、Developer Preview 自动升级，以及把旧 Reasonix 二进制误当授权。

阻断证据：官方 repository/license/integrity 三方一致；入口开工时不存在；AGENTS 修改前再次核对基线 SHA；lock 固定；真实 Seatbelt 写入被拒；ask 无 answerer 返回 unavailable；headless 明确停在 MISSING_CREDENTIAL；Reasonix 路由已退役。

修正与闭环：新增全局 `read-only + ask` patch；AGENTS 禁用写任务路由和自动调用；版本变化强制重验；保留可回滚备份与固定清单。

## 残余风险

1. **未完成真实模型闭环**：CLI 可用不等于 DeepSeek 模型可用；当前不得自动调用。
2. **Node engine 警告**：传递依赖 `@earendil-works/pi-ai@0.82.1` 声明 Node `>=22.19.0`，本机为 `22.16.0`。CLI 当前能运行，但未来路径可能触发不兼容；本任务未获授权升级系统 Node。
3. **Developer Preview**：破坏性变更风险高，固定版本之外不得静默升级。
4. **磁盘余量低**：最终约 `12.4 GiB` 可用、占用 `94%`。本任务只做限域审计，未删除其他数据。
5. **现有 DSH_HOME**：其凭证与历史状态由用户所有，本任务未读取或迁移；后续真实闭环必须由用户确认其用途。
6. **cwd 路由风险**：必须从精确项目根调用，否则默认 workspace 会错误。

## 用户唯一下一步

由用户本人确认并完成 DeepSeek 凭证配置（不要在聊天中粘贴任何 secret），然后在**新建或重启后的 Codex 会话**中，明确授权一次无敏感数据、只读、无外部写入的真实 headless 验证。验证通过前保持“已安装待启用”。

