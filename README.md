# 艺人经纪工作台 · 线索雷达数据源

这个仓库只放一个文件 `leads-data.json`，它是「艺人经纪工作台」网页里「📡 线索雷达」的数据源。

页面打开时会自动 fetch 这个文件（每 6 小时最多拉一次），点「🔄 检查更新」可立即拉取。所以：**定时任务只要改写这个文件，手机和电脑上打开网页就会自动更新，不需要重新发布网页**。

## 数据结构

```json
{
  "generatedAt": "2026-11-21T21:05:00",
  "demo": false,
  "items": [
    {
      "id": "稳定的唯一键（建议用 url 的哈希或固定编号）",
      "title": "线索标题",
      "org": "主办/需求方",
      "area": "华东",
      "region": "杭州",
      "category": "音乐节",
      "deadline": "2026-12-15",
      "budget": "80-150 万",
      "summary": "一句话说清需求",
      "score": 86,
      "url": "https://…原文链接",
      "tags": ["现场演出"],
      "source": "采集来源",
      "publishedAt": "2026-11-20",
      "demo": false
    }
  ]
}
```

## 三条硬规则

1. `generatedAt` 必须是 `YYYY-MM-DDTHH:MM:SS` 格式，页面用它判断新旧、提醒数据过期。
2. `id` 必须稳定：同一条线索每次更新沿用同一个 id，否则页面里「已读 / 已忽略 / 已转客户」的记录会被当成新线索重新出现。
3. 真实数据把 `demo` 设为 `false`（或整字段去掉），否则页面会提示「示例数据」。

## 定时任务怎么写这个文件

任选其一：

- **GitHub API**：`PUT https://api.github.com/repos/xiaoyang29/artist-workbench-feed/contents/leads-data.json`，带上 `Authorization: Bearer <token>`、`branch: main`，更新时还要带上次返回的 `sha`。
- **gh CLI**：`gh api repos/xiaoyang29/artist-workbench-feed/contents/leads-data.json -X PUT -f message=update -f content=$(base64 -w0 leads-data.json) -f branch=main`（更新时加 `-f sha=<blob_sha>`）。

页面实际读取的地址（按顺序尝试，前一个失败自动换下一个）：

1. `https://cdn.jsdelivr.net/gh/xiaoyang29/artist-workbench-feed@main/leads-data.json`（国内较快，缓存约 12 小时）
2. `https://raw.githubusercontent.com/xiaoyang29/artist-workbench-feed/main/leads-data.json`（实时，但国内可能不稳）

刚更新完若发现还是旧数据，等一会儿或换下一路即可；点「🔄 检查更新」会立刻重拉。
