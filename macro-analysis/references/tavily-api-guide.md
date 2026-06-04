# Tavily API 使用指南

## SDK 安装

```bash
pip install tavily-python
```

## Method A: Python SDK（首选）

```python
import os
from tavily import TavilyClient

client = TavilyClient(api_key=os.getenv("TAVILY_API_KEY"))

# 基础搜索
result = client.search(
    query="本周 CPI PCE GDP 非农 FOMC 经济数据",
    search_depth="advanced",   # basic | advanced
    max_results=5,             # 1-10
    include_answer=True,       # 返回 AI 摘要
    include_images=False,
)

# 提取结果
for r in result.get("results", []):
    print(f"标题: {r['title']}")
    print(f"URL: {r['url']}")
    print(f"内容: {r['content'][:200]}...")
    print("---")

# AI 摘要
print("AI 摘要:", result.get("answer", "无"))
```

## Method B: curl REST API

```bash
TAVILY_API_KEY="${TAVILY_API_KEY:?must be set}"

curl -s -X POST https://api.tavily.com/search \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "'"$TAVILY_API_KEY"'",
    "query": "本周 CPI PCE GDP 非农 FOMC 经济数据",
    "search_depth": "advanced",
    "max_results": 5,
    "include_answer": true
  }' | python3 -m json.tool
```

## Method C: Python requests (无 SDK)

```python
import os, requests

response = requests.post(
    "https://api.tavily.com/search",
    json={
        "api_key": os.getenv("TAVILY_API_KEY"),
        "query": "本周 CPI 非农 FOMC 经济数据",
        "search_depth": "advanced",
        "max_results": 5,
    },
    timeout=30,
)
result = response.json()
```

## 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `query` | string | (必填) | 搜索关键词 |
| `search_depth` | string | `basic` | `basic` 快速搜索，`advanced` 深度搜索（更多结果） |
| `max_results` | int | 5 | 返回结果数，1-10 |
| `include_answer` | bool | false | 是否返回 AI 生成的答案摘要 |
| `include_images` | bool | false | 是否包含图片结果 |
| `include_raw_content` | bool | false | 是否包含网页原始内容 |
| `days` | int | 3 | 搜索最近 N 天的内容 |
| `topic` | string | `general` | `general` 或 `news`（新闻类） |

## 错误码与处理

| HTTP Status | 含义 | 处理方式 |
|---|---|---|
| 200 | 成功 | 正常处理 |
| 400 | 参数错误 | 检查 `query` 是否为空，`max_results` 是否超限 |
| 401 | API Key 无效 | 提示用户重新设置 `TAVILY_API_KEY` |
| 429 | Rate limit | 等待 5s 后重试（最多 3 次），或切换到 WebSearch fallback |
| 500 | 服务端错误 | 等待 3s 后重试 1 次，仍失败则切换到 WebSearch fallback |
| 超时 | 网络问题 | 检查网络连接，增加 timeout 值，或切换到 WebSearch |

## Rate Limit

- 免费版: ~100 次/月
- 付费版: 按计划不同

超出限制时自动降级到内置 WebSearch 工具。

## 最佳实践

1. **并行搜索:** 4 个维度的搜索相互独立，建议并行发送
2. **缓存结果:** 同一 query 在 5 分钟内不重复请求
3. **关键词优化:** 加上日期限定（如 "2026年6月 第1周 CPI"）提高精度
4. **来源验证:** 交叉对比至少 2 个来源确认关键数据
