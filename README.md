# 艺人经纪工作台 · 线索雷达数据源

这个仓库只放一个文件 `leads-data.json`，它是「艺人经纪工作台」网页里「📡 线索雷达」的数据源。

页面打开时会自动拉取这个文件（每 6 小时最多拉一次），点「🔄 检查更新」可立即拉取。所以：**定时任务只要改写这个文件，手机和电脑上打开网页就会自动更新，不需要重新发布网页**。

## 页面读取的地址（按顺序尝试，前一个拉不到或还是旧数据就自动换下一个）

1. `https://cdn.jsdelivr.net/gh/xiaoyang29/artist-workbench-feed/leads-data.json`（**不要加 @main**：带版本号的地址会被 CDN 缓存很久，改了文件半天还是旧的）
2. `https://raw.githubusercontent.com/xiaoyang29/artist-workbench-feed/main/leads-data.json`

## 数据结构

```json
{
  "generatedAt": "2026-11-21T21:52:58+08:00",
  "demo": false,
  "items": [
    {
      "id": "稳定的唯一键（建议用公告编号，如 ccgp-27699781）",
      "title": "线索标题",
      "org": "主办/需求方",
      "area": "华东",
      "region": "浙江·杭州",
      "category": "音乐节",
      "deadline": "2026-12-15",
      "budget": "80-150 万",
      "summary": "一句话说清需求",
      "score": 86,
      "url": "https://…原文链接",
      "tags": ["现场演出"],
      "source": "中国政府采购网",
      "publishedAt": "2026-11-20",
      "demo": false
    }
  ]
}
```

## 四条硬规则

1. `generatedAt` 用 `YYYY-MM-DDTHH:MM:SS+08:00`，页面靠它判断新旧和提醒数据过期。
2. `id` 必须稳定：同一条线索每次更新沿用同一个 id（公告编号最稳），否则网页里「已读 / 已忽略 / 已转客户」的记录会被当成新线索重新出现。
3. 真实数据把 `demo` 设为 `false`，否则页面会挂「示例数据」提示。
4. `category` 只用这几个值：文旅/城市推广、音乐节、夜店/酒吧、剧院/剧场、开业/庆典、商演/活动、品牌代言、影视综艺、直播电商；`area` 只用华北、东北、华东、华中、华南、西南、西北。

## 定时任务怎么写这个文件

- **GitHub API**：先 `GET .../contents/leads-data.json` 拿 `sha`，再 `PUT` 同一路径，body 带 `message`、`content`（base64）、`branch: main`、`sha`。
- **gh CLI**：`gh api repos/xiaoyang29/artist-workbench-feed/contents/leads-data.json -X PUT -f message=update -f content=$(base64 -w0 leads-data.json) -f branch=main -f sha=<blob_sha>`。
