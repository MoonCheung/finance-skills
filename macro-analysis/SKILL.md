---
name: macro-analysis
description: >
  生成全球市场核心交易策略周报，通过 Tavily API 联网搜索实时宏观经济数据与事件。
  触发: "宏观分析", "宏观周报", "交易策略周报", "全球市场周报", "宏观对冲",
  "本周操作建议", "市场周报", "策略周报", "美联储分析", "CPI周报", "非农前瞻",
  "央行会议分析", "地缘政治分析", "macro weekly", "global macro report",
  "weekly trading strategy", "本周宏观", "宏观交易", "利率分析", "汇率分析",
  "经济数据周报", "FOMC前瞻", "ECB分析", "大盘策略", "资产配置周报",
  任何需要联网获取实时宏观数据并生成结构化策略报告的任务。
metadata:
  hermes:
    tags: [Finance, Macro, Trading, Weekly Report, Global Markets]
    category: macro-analysis
    env_vars:
      - TAVILY_API_KEY
---

# Macro Analysis Skill

利用 Tavily API 联网搜索实时全球宏观经济数据，生成《全球市场核心交易策略周报》。

## Defaults

| Parameter | Default | Description |
|---|---|---|
| 报告类型 | 周报 | 可选: 日报, 周报, 专题分析 |
| 时间范围 | 本周 | 基于当前日期自动计算 |
| 关注区域 | 全球 | 可选: 美国, 亚太, 欧洲, 新兴市场 |
| 资产类别 | 全资产 | 可选: 股票, 债券, 外汇, 商品, 加密货币 |
| 搜索深度 | advanced | 可选: basic, advanced（Tavily 参数） |
| 最大搜索条数 | 5 | 每维度搜索结果数 |

---

## Step 1: Ensure the API Key Is Available

**目标:** Current environment status

```bash
!`python3 - <<'PY'
import os
print("TAVILY_API_KEY_SET" if os.getenv("TAVILY_API_KEY") else "TAVILY_API_KEY_MISSING")
PY`
```

If `TAVILY_API_KEY_MISSING`, ask the user to set:

```bash
echo 'export TAVILY_API_KEY="tvly-dev-_..."' >> ~/.zshrc && source ~/.zshrc
```
> Bash 用户将 `~/.zshrc` 替换为 `~/.bashrc`。

> 详细 API 使用指南和代码模板见 `references/tavily-api-guide.md`

---

## Step 2: 广度数据扫描 (Data Scan)

**目标:** 用 Step 1 确定的方法，对 4 个维度并行搜索。

| # | 维度 | 搜索关键词模板 |
|---|---|---|
| 1 | 宏观日历 | `"本周 {date} 关键经济数据 CPI PCE GDP 非农 FOMC 央行讲话"` |
| 2 | 地缘政治 | `"当前地缘政治事件 中东 贸易谈判 供应链 油价 影响 {date}"` |
| 3 | 企业财报 | `"本周 {date} 财报发布 科技股 半导体 能源 权重股"` |
| 4 | 市场情绪 | `"VIX 恐慌指数 加密贪婪恐慌指数 {date}"` |

**Method A (Python SDK):** `references/tavily-api-guide.md`
**Method B (curl):** `references/tavily-api-guide.md`
**Fallback:** 使用内置 WebSearch 工具，对每个维度执行搜索。

**Gate:** ≥3/4 维度必须返回可用数据。缺失维度在报告中标记 `⚠️ 数据不足`。

---

## Step 3: 逻辑链推演 (Critical Thinking)

**目标:** 将数据点连接为可操作的 if/then 逻辑链。不罗列新闻，建立传导路径。

每条逻辑链格式: `如果 [条件] → 那么 [资产影响] → 进而 [传导效应]`

**最少构建 3 条逻辑链，覆盖:**

| 类别 | 示例 |
|---|---|
| 宏观数据 | 如果 CPI 超预期 → 那么 美债收益率↑ → 进而 QQQ 承压 |
| 地缘政治 | 如果 谈判破裂 → 那么 XLE↑ → 同时 GLD↑ |
| 企业财报 | 如果 科技巨头指引不及预期 → 纳指回调 → BTC 流动性溢价收缩 |
| 央行政策 | 如果 FOMC 偏鹰 → USD↑ → EEM 资金外流 |

**Gate:** ≥3 条逻辑链。数据不足以构建某条链时，标记 `数据不足，待观察`。

> 详见 `references/macro-framework.md`

---

## Step 4: 输出结构化报告 (Output Format)

**目标:** 严格按以下模板生成《全球市场核心交易策略周报》。所有字段必须填充，缺失数据用 `数据不足` 替代。

### 1. 核心结论 (Core Thesis)

| 确定性 | Emoji |
|---|---|
| 高确定 | 🎯确信 |
| 需警惕 | ⚠️警惕 |
| 防守型 | 🛡️防守 |

| 维度 | 内容要求 |
|---|---|
| 最确定的机会 | [资产/代码] + [宏观+技术+催化剂] |
| 最大的风险 | [事件] + [应对: 收紧止损/对冲] |
| 现金仓位建议 | [百分比] + [理由] |
| Crypto 关注 | [BTC/ETH 宏观流动性判断] |

### 2. 本周操作节奏 (Weekly Rhythm)

| 日期 | 关键事件 | 预期影响与操作建议 |
|---|---|---|
| 周一 | [事件] | [观察点] |
| 周二 | [财报/数据] | [若 X → 做多 Y; 否则 → 做空 Z] |
| ... | ... | ... |
| 周五 | [数据/事件] | [周末风控建议] |

### 3. 批判性风控 (Devil's Advocate)

- **反直觉思考:** 市场一致性预期？如果市场错了，最大痛点在哪？
- **黑天鹅预演:** 本周最可能被忽视的风险点？

**Gate:** 3 大板块全部填充。搜索不完备处标记 `⚠️ 数据不足`。

> 完整报告范例见 `references/report-example.md`

---

## Constraints

- **必须联网核实:** 财报日期、宏观数据发布时间 100% 准确，每条关键数据附来源链接
- **Ticker 明确:** 提及板块时给出 ETF 或龙头股代码（如 XLE, NVDA, GOOG, COIN）
- **拒绝模棱两可:** 给出关键观察点位 (Trigger Point)，不说"可能涨也可能跌"
- **免责声明:** 报告末尾注明 "⚠️ 本报告仅供参考，不构成投资建议。投资有风险，入市需谨慎。"

---

## Reference Files

- `references/tavily-api-guide.md` — Tavily API 完整使用指南：SDK 安装、REST 端点、参数说明、错误码、rate limit
- `references/report-example.md` — 完整报告范例：一篇真实数据填充的周报
- `references/macro-framework.md` — 宏观分析框架：关键指标解读逻辑、传导机制、历史参照
