# 🦌 小鹿 · Apple Watch 健康助手

> 你的 AI 健康小伙伴，每天早 8 点准时报到，帮你读懂自己的身体。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Built with OpenClaw](https://img.shields.io/badge/Built%20with-OpenClaw-purple)](https://openclaw.ai)
[![Data: Apple Watch](https://img.shields.io/badge/Data-Apple%20Watch-black?logo=apple)](https://www.apple.com/apple-watch/)
[![Channel: Hi](https://img.shields.io/badge/Push-小红书%20Hi-ff2442)](https://www.xiaohongshu.com)

---

## 小鹿是什么

小鹿是一个跑在 [OpenClaw](https://openclaw.ai) 上的个人 AI 助手，专注**健康数据分析**。

它每天从你的 Apple Watch 读取数据，用你能看懂的语言告诉你身体状态——不装医生，不说废话，像朋友一样聊。

```
Apple Watch
    ↓  每周导出
iPhone 健康 App → health-export.zip
    ↓  mac_push_health.sh
GitHub 私有数据仓（xiaolu-data）
    ↓  心跳触发
OpenClaw · 小鹿分析引擎
    ↓  每天 08:00
小红书 Hi → 你的手机
```

---

## ✨ 功能

### 💓 每日健康报告
每天早 8 点自动分析，推送到小红书 Hi：

```
🦌 早安！今天是 03/29 周日

━━━━━━━━━━━━━━━━━━
💓 HRV   43.9ms  ← 比基线高 +12ms 🟢
❤️  静息心率  74bpm  ← 正常范围
👟 步数   8,234步  ← 昨日
😴 睡眠   未佩戴
━━━━━━━━━━━━━━━━━━
节律评分：B+（80分）

今天状态不错，09:00-10:00 是你的黄金时段
适合安排需要专注的工作～
```

### 📈 节律分析
根据历史 HRV 数据，找出你的**最佳时段**和**低谷时段**：

| 时段 | HRV均值 | 状态 |
|------|---------|------|
| 09:00-10:00 | 47ms | 🟢 黄金时段 |
| 17:00-18:00 | 43ms | 🟢 次优 |
| 12:00-13:00 | 24ms | 🔴 低谷，避免重要决策 |
| 20:00-21:00 | 27ms | 🟡 恢复中 |

### 🗓️ 会议室一键预订
直接跟小鹿说，它去搞定：

```
你：帮我订明天下午4点会议室
小鹿：✅ 已订 26F·2603摄影（3人间）
      会议 ID：6076893
```

### 📚 待读清单
发链接给小鹿，它帮你：
- 自动解析摘要（1-2句）
- 问你目的（项目/赛道/感兴趣）
- 定时提醒（上午收→12点提醒，下午收→16点提醒）
- 附带全网同赛道参考阅读 3 条

### 🧠 记忆功能
```
你：记下来，今天喝了2L水
小鹿：记住了，明天报告里提醒你 ✓
```

---

## 🏗️ 架构

```
┌─────────────────────────────────────────────────────┐
│                     小鹿系统                          │
├──────────────┬──────────────────┬────────────────────┤
│  数据层       │   分析层          │   推送层            │
│              │                  │                    │
│ Apple Watch  │ analyze_health   │ OpenClaw           │
│     ↓        │       .py        │   message tool     │
│ iPhone 健康  │                  │      ↓             │
│     ↓        │ ・HRV 基线       │ 小红书 Hi           │
│ 每周手动导出  │ ・节律评分        │                    │
│     ↓        │ ・状态判断        │                    │
│ GitHub       │ ・个性化建议      │                    │
│ xiaolu-data  │                  │                    │
└──────────────┴──────────────────┴────────────────────┘

┌─────────────────────────────────────────────────────┐
│                   Skills 体系                         │
├────────────────┬────────────────┬────────────────────┤
│ xiaolu-adviser │ xiaolu-meeting │ xiaolu-readlist    │
│ 健康分析报告    │ 会议室预订      │ 待读清单提醒         │
└────────────────┴────────────────┴────────────────────┘
```

---

## 📁 目录结构

```
xiaolu-health/
├── scripts/
│   ├── analyze_health.py      # 核心分析引擎
│   ├── parse_health_xml.py    # Apple Health XML 解析
│   ├── mac_push_health.sh     # Mac 端一键推送脚本
│   ├── log_meal.py            # 饮食记录
│   ├── memory_notes.py        # 备忘/提醒管理
│   └── update_and_report.sh   # 更新并立即生成报告
├── skills/
│   ├── xiaolu-adviser/        # 健康报告 Skill
│   ├── xiaolu-meeting/        # 会议室预订 Skill
│   └── xiaolu-readlist/       # 待读清单 Skill
├── docs/
│   └── PRD.md                 # 产品需求文档
└── README.md
```

---

## 🚀 快速开始

### 前置条件
- Apple Watch（任意型号）
- iPhone + Apple 健康 App
- Mac（用于导出数据）
- [OpenClaw](https://openclaw.ai) 账号
- GitHub 账号（存放数据）

### 1. Fork 仓库

```bash
# Fork 本仓库到你的账号
# 同时创建私有数据仓库
gh repo create your-name/xiaolu-data --private
```

### 2. 配置环境变量

在 OpenClaw workspace 的 `openclaw.json` 中配置：

```json
{
  "channels": {
    "hiredcity": {
      "userId": "your@xiaohongshu.com"
    }
  }
}
```

在 Mac 上配置 `mac_push_health.sh`：

```bash
GITHUB_USER="your-github-username"
GITHUB_REPO="xiaolu-data"
GITHUB_TOKEN="ghp_xxxxxxxxxxxx"
```

### 3. 首次导出数据

```
iPhone → 健康 App → 右上角头像 → 导出所有健康数据
→ 保存 export.zip 到 Mac 桌面
→ 运行 mac_push_health.sh
```

### 4. 配置 HEARTBEAT.md

复制 `docs/HEARTBEAT.md` 到你的 OpenClaw workspace，开启每日自动报告。

---

## 📊 数据说明

### 重点指标

**HRV（心率变异性）** — 最重要的指标
- 反映自主神经系统平衡状态
- 越高越好，但关键看**趋势**而非绝对值
- 低于7日基线 5ms+ → 可能疲劳/压力大
- 高于7日基线 5ms+ → 恢复良好，状态佳

**静息心率**
- 正常范围 60-100 bpm
- 长期偏高 → 关注睡眠和压力
- 运动员通常 40-60 bpm

**节律评分**
| 等级 | 分数 | 含义 |
|------|------|------|
| S | 90-100 | 最佳状态，全力冲 |
| A | 80-89 | 状态好，正常安排 |
| B+ | 75-79 | 不错，注意节奏 |
| B | 65-74 | 一般，避免过度消耗 |
| C | <65 | 注意休息恢复 |

---

## 🔒 隐私说明

- 健康数据存储在**你自己的私有 GitHub 仓库**，小鹿不持有任何数据
- OpenClaw 仅在分析时读取数据，不做持久化存储
- 所有推送通过小红书 Hi，仅你可见

---

## 🤝 Contributing

欢迎 PR！特别需要：
- 更多健康指标支持（VO2 Max、血氧等）
- 更好的图表可视化方案
- iPhone Shortcuts 自动推送方案

---

## 📄 License

MIT © 2026 小鹿项目

---

<div align="center">
  <br>
  🦌 <strong>小鹿</strong> · 每天陪你把脉，不装医生，只做朋友
  <br><br>
  Built with ❤️ on <a href="https://openclaw.ai">OpenClaw</a>
</div>
