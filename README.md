# B站数据接口（Bilibili API）：视频搜索、评论、UP 主、热门、字幕

**B站数据 API · 哔哩哔哩视频搜索 · B站评论采集 · UP 主数据 · B站热门榜 · B站字幕。**

这个仓库是 [okdata](https://okdata.online/zh/?src=gh-bilibili-api) 的B站接口示例：9 个接口在线可用，一个 API Key 调用，按次计费（$0.002 起），失败不扣费。不用登录平台账号，不用 Cookie，不开浏览器，所以也不存在账号被风控的问题。

[免费注册拿 Key（送 $0.1 试用额度）](https://okdata.online/zh/signup/?src=gh-bilibili-api) · [全部接口文档](https://okdata.online/zh/apis/?src=gh-bilibili-api) · [接入 AI（MCP）](https://okdata.online/zh/mcp/?src=gh-bilibili-api)

## 接口清单

| 接口 | 说明 | 价格 | 路径 |
|---|---|---|---|
| [B站视频搜索](https://okdata.online/zh/apis/bilibili/videos/search/) | 按关键词搜视频，带播放、弹幕、点赞和投币。 | $0.002 / 次 | `GET /v1/bilibili/videos/search` |
| [B站视频详情](https://okdata.online/zh/apis/bilibili/videos/get/) | 按 BV 号或链接取一个视频。 | $0.002 / 次 | `GET /v1/bilibili/videos/get` |
| [B站视频评论](https://okdata.online/zh/apis/bilibili/videos/comments/list/) | 视频评论，按最热或最新。 | $0.002 / 次 | `GET /v1/bilibili/videos/comments/list` |
| [B站热搜榜](https://okdata.online/zh/apis/bilibili/trends/list/) | B站当前的热搜词，最多 50 个，带名次和热度。 | $0.002 / 次 | `GET /v1/bilibili/trends/list` |
| [B站用户资料](https://okdata.online/zh/apis/bilibili/users/get/) | 按用户 ID 取 B 站账号：昵称、签名、头像、认证和认证说明、粉丝、关注、投稿视频数、累计获赞、等级和直播间链接。 | $0.002 / 次 | `GET /v1/bilibili/users/get` |
| [B站用户投稿](https://okdata.online/zh/apis/bilibili/users/videos/list/) | 账号投稿的视频，从新到旧，每页 30 条：标题、简介、播放、评论、弹幕、时长和发布时间。 | $0.002 / 次 | `GET /v1/bilibili/users/videos/list` |
| [B站评论回复](https://okdata.online/zh/apis/bilibili/comments/replies/list/) | 按评论 ID 和所在视频的 BV 号取回复：内容、回复者、点赞、回复的是哪一条（parent_id）、发布时间和 IP 属地。 | $0.002 / 次 | `GET /v1/bilibili/comments/replies/list` |
| [B站综合热门](https://okdata.online/zh/apis/bilibili/videos/trending/) | B站综合热门视频，每页 20 条：标题、简介、UP 主、播放、点赞、投币、收藏、弹幕、时长、分区、IP 属地和推荐理由。 | $0.002 / 次 | `GET /v1/bilibili/videos/trending` |
| [B站用户投稿（备用版）](https://okdata.online/zh/apis/bilibili/backup/users/videos/list/) | B站用户投稿（bilibili.users.videos.list）的备用版，原接口取不到时改调：账号投稿的视频，从新到旧，每页 20 条（这条数据通道每页就是 20 条），价格略高。标题、封面、播放、弹幕、时长和发布时间；没有简介、点赞和评论数。用户不存在时和没有投稿一样，返回空结果，不报 404。 | $0.0025 / 次 | `GET /v1/bilibili/backup/users/videos/list` |

## 快速开始

1. 在 [okdata 控制台](https://okdata.online/zh/signup/?src=gh-bilibili-api) 用邮箱注册，拿到 `okd_` 开头的 Key。
2. 调一次接口：

```bash
export OKDATA_API_KEY=okd_...
curl -G "https://api.okdata.online/v1/bilibili/videos/search" \
  -H "Authorization: Bearer $OKDATA_API_KEY" \
  --data-urlencode 'query=Claude Code 教程'
```

Python 版在 [examples/quickstart.py](examples/quickstart.py)（只用标准库，会顺带演示翻页）：`python3 examples/quickstart.py`。

返回统一的结构：列表在 `data.items`，下一页用 `page.next_cursor` 传回 `cursor`，这次扣了多少钱在 `meta.billing.amount`，平台不提供的字段为 null 并在 `meta.unavailable` 里写明原因。

## 让 AI 直接用（MCP）

Claude Code：

```bash
claude mcp add --transport http --scope user okdata https://api.okdata.online/mcp --header "Authorization: Bearer $OKDATA_API_KEY"
```

Cursor、Cherry Studio、WorkBuddy 等支持远程 MCP 的客户端：

```json
{
  "mcpServers": {
    "okdata": {
      "url": "https://api.okdata.online/mcp",
      "headers": {
        "Authorization": "Bearer okd_..."
      }
    }
  }
}
```

接好以后直接用中文说需求，比如「B站视频搜索：Claude Code 教程」。AI 会先用免费的 `find`、`describe` 找接口和看价格，再用 `call` 取数据。

## 常见问题

**要登录平台账号吗？** 不用。数据是公开内容，okdata 在服务端获取，你的账号完全不参与。

**怎么收费？** 每个接口单独定价，表里写的就是一次调用的价格；按条计费的接口按实际返回条数算。调用失败（包括超时）不扣费。新账号送 $0.1 试用额度，之后按需充值，没有订阅。价格页：https://okdata.online/zh/pricing/

**和官方开放平台有什么区别？** 官方开放平台只能读授权给你的账号自己的数据，而且要企业资质和审核；这里是平台上任何人都能看到的公开数据，比如搜索结果、别人的笔记或视频、评论、热榜。

**能翻页吗？** 列表接口每页一次调用，把 `page.next_cursor` 原样传回 `cursor` 就是下一页，24 小时内有效。

**出错了怎么办？** 每个错误都有固定的 `code`、中文说明 `message_zh`，以及会不会扣费（`charged`）和要不要重试（`retry`）。全部错误码：https://okdata.online/zh/docs/errors/

## English

Examples for the okdata Bilibili API: 9 live endpoints behind one API key, pay per call from $0.002, failed calls free, no platform login or cookies. Sign up at https://okdata.online/signup/?src=gh-bilibili-api, docs at https://okdata.online/apis/, MCP at https://okdata.online/mcp/.

## License

MIT. The data comes from the okdata API; its terms apply to API use.
