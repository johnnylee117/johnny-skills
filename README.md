# 🧰 Johnny Skills

#### 面向真实工程场景的一组 AI Agent Skills

这里收集可被 Codex、Claude Code 和其他兼容 Agent Skills 标准的 Agent 直接加载的技能。每个目录都是一个独立 skill：下载、安装或让 Agent 引用该目录即可使用。

[English](README.en.md)

---

## 📋 Skills

| Skill | 一句话说明 | 适用场景 |
| --- | --- | --- |
| 🚌 [canoe-capl-fuzz-monitor](https://github.com/johnnylee117/johnny-skills/tree/main/canoe-capl-fuzz-monitor) | 为授权 CAN/CAN-FD 鲁棒性测试构建证据优先的被动 CANoe CAPL 监测器。 | `.cfg`、DBC、多通道观测、超时、计数器、状态与角度异常、BLF 证据关联。 |

---

## 📦 安装

在支持 Agent Skills 的工具中，直接把下面这句话发给你的 Agent：

```text
帮我安装这个 skill：https://github.com/johnnylee117/johnny-skills/tree/main/canoe-capl-fuzz-monitor
```

也可以手动把对应目录放入 Agent 的 skills 目录；仓库里的每个一级目录都是独立、可安装的 skill。

---

## 🚌 CANoe CAPL Fuzz Monitor

> 不猜通道映射，不把总线现象包装成物理结论。

这个 skill 用于构建、修复和审查 CANoe CAPL 监测逻辑。它从当前打开的 CANoe 配置和实际挂载的 DBC 出发，要求将通道、数据库别名、消息和信号逐项核对，再建立可回放、可解释的监测证据。

它覆盖：

- 多 CAN 通道的被动原始帧观测与 CAPL 分发；
- DBC 驱动的信号解码、枚举解释与滚动计数器检查；
- 手动布防、稳定窗口、基线、超时与去抖规则；
- Write Window、事件日志与 BLF 上下文的证据记录；
- 在真实 CANoe 节点上下文中编译并验证 CAPL。

### 安全边界

它只处理**被动监测和验证**，不会生成、发送、放大或调度 fuzz、DoS、replay 流量。仅应在获得明确授权的测试资产、网络与系统上使用。

### 这样触发

```text
Use $canoe-capl-fuzz-monitor to create or repair a CANoe CAPL monitor from this cfg and DBC set.
```

```text
请用 $canoe-capl-fuzz-monitor 审查这个 CAPL 监测器。
当前打开的配置是 VehicleTest.cfg，CAN1 观测 EPS1，CAN3 观测 HWA1。
需要人工布防后记录 HealthState、超时、滚动计数器和转向角异常。
```

→ [查看完整 SKILL.md](canoe-capl-fuzz-monitor/SKILL.md)

---

## 🤝 贡献

欢迎补充经过验证的 CANoe/CAPL 兼容性说明、通用工作流或新的独立 skill。请不要提交专有 DBC、客户配置、BLF、凭据或未经授权的测试数据。

## 📄 License

[MIT](LICENSE)
