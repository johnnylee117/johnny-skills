# 🧰 Johnny Skills

#### Practical AI Agent Skills for real engineering work

This repository collects standalone skills that Codex, Claude Code, and other Agent Skills-compatible agents can load directly. Every top-level skill directory is independently installable.

[中文](README.md)

---

## 📋 Skills

| Skill | Summary | Use it for |
| --- | --- | --- |
| 🚌 [canoe-capl-fuzz-monitor](https://github.com/johnnylee117/johnny-skills/tree/main/canoe-capl-fuzz-monitor) | Build evidence-first passive CANoe CAPL monitors for authorized CAN/CAN-FD robustness tests. | Active `.cfg` files, attached DBCs, cross-channel observation, timeouts, counters, state/angle checks, and BLF evidence. |

---

## 📦 Install

In an Agent Skills-compatible tool, ask your agent:

```text
Install this skill: https://github.com/johnnylee117/johnny-skills/tree/main/canoe-capl-fuzz-monitor
```

You can also copy a skill directory into your agent's skills location. Each top-level directory in this repository is one standalone skill.

---

## 🚌 CANoe CAPL Fuzz Monitor

> Verify the mapping. Preserve the evidence. Do not overstate what the bus proves.

This skill builds, repairs, and reviews CANoe CAPL monitor logic. It starts from the configuration actually open in CANoe and the DBCs attached to it, then establishes an auditable channel → database → message → signal mapping before implementing detectors.

It covers passive multi-channel frame observation, DBC-backed decoding, manual arming and baselines, timeout/debounce/counter rules, readable event evidence, and validation in the real CAPL-node context.

### Safety boundary

This is a **monitoring and validation** skill. It does not generate, transmit, amplify, or schedule fuzz, DoS, or replay traffic. Use it only with explicitly authorized systems and test assets.

### Invoke it

```text
Use $canoe-capl-fuzz-monitor to create or repair a CANoe CAPL monitor from this cfg and DBC set.
```

→ [Read the full SKILL.md](canoe-capl-fuzz-monitor/SKILL.md)

---

## 🤝 Contributing

Contributions are welcome for verified CANoe/CAPL compatibility notes, reusable workflows, and additional standalone skills. Do not submit proprietary DBCs, customer configurations, BLFs, credentials, or unauthorized test data.

## 📄 License

[MIT](LICENSE)
