# Macro Analysis - 全球市场核心交易策略周报

基于 Tavily API 联网搜索实时全球宏观经济数据，自动生成结构化的《全球市场核心交易策略周报》。

## 快速开始

### 1. 设置 API Key

**方法一：Python 交互输入（临时）**
```bash
python3 - <<'PY'
import os
key = input("Enter your TAVILY_API_KEY: ").strip()
if key:
    os.environ["TAVILY_API_KEY"] = key
    print("Done.")
PY`
```

**方法二：写入 Shell 配置（永久）**
```bash
echo 'export TAVILY_API_KEY="tvly-dev-_..."' >> ~/.zshrc && source ~/.zshrc
```

> 免费获取 API Key: [Tavily Documentation](https://docs.tavily.com/documentation/api-reference/introduction)

### 2. 使用方式

在 Claude Code 中输入以下任意触发词即可激活：

```
宏观周报 | 交易策略周报 | 全球市场周报 | 美联储分析 | CPI周报 | 非农前瞻
```

或直接用自然语言：

```
帮我生成一份本周的全球市场交易策略周报
分析一下这周美联储有什么动作，对市场有什么影响
```

## 功能概览

| 步骤 | 说明 |
|---|---|
| **Step 1: Key 检测** | 自动检测 `TAVILY_API_KEY` 是否可用 |
| **Step 2: 数据扫描** | 并行搜索 4 个维度：宏观日历、地缘政治、企业财报、市场情绪 |
| **Step 3: 逻辑推演** | if/then 传导链，连接数据点到资产影响 |
| **Step 4: 输出报告** | 结构化周报：核心结论 + 操作节奏 + 风控反思 |

## 输出示例

```
## 全球市场核心交易策略周报 (2026年6月1日-5日)

### 1. 核心结论 🎯确信
- 最确定的机会: Long XLE — 中东局势升级 + 原油库存连降
- 最大的风险: FOMC 偏鹰 — QQQ 仓位减半对冲
- 现金仓位建议: 25%
- Crypto 关注: BTC 看涨 — 宏观流动性改善

### 2. 本周操作节奏
| 周一 | 中国PMI | 若>50.5 → Long FXI |
| 周二 | JOLTS   | 若>8.5M → 做空 TLT |
...

### 3. 批判性风控
- 反直觉思考: 市场一致性预期是什么？
- 黑天鹅预演: 本周最可能被忽视的风险点？
```

## 参考文档

| 文件 | 内容 |
|---|---|
| `references/tavily-api-guide.md` | API 使用指南（SDK/curl/requests 三种方法、参数说明、错误码） |
| `references/macro-framework.md` | 宏观分析框架（关键指标解读、传导机制、季度节奏、事件评分） |
| `references/report-example.md` | 完整周报范例 |

## 免责声明

⚠️ 本报告仅供参考，不构成投资建议。投资有风险，入市需谨慎。
