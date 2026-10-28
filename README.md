# 背诵小猪手 · 云端卡组仓库

这是「背诵小猪手」App 的云端卡组来源。App 会读取本仓库 `decks/` 目录下的卡组文件，并在「导入笔记 → 从云端导入」中显示文件大小、上传时间、上传人、文件说明。

> 仓库是公开的。请勿上传包含隐私、密钥、未授权资料的内容。

## 目录格式

```text
decks/
└── 任意分类目录/
    ├── 卡组文件.md / .txt / .zip
    └── 卡组文件.md.meta.json
```

- 卡组文件必须放在 `decks/` 下，可以继续按学科/来源建子目录；
- 每个卡组文件必须有一个**同目录、同名 + `.meta.json`** 的信息文件；
- 支持 `.md`、`.markdown`、`.txt`、`.zip`；
- 有图片时建议打包成 `.zip`（Markdown 与 `images/` 一起），App 会自动解出图片；
- `README.md` 不会作为卡组导入（App 只扫描 `decks/` 下的卡组文件）。

## 信息文件格式

以 `decks/针灸与人文/穴位定位.md` 为例，信息文件必须是：

```text
decks/针灸与人文/穴位定位.md.meta.json
```

内容：

```json
{
  "file": "穴位定位.md",
  "id": "acupoint-location",
  "version": 1,
  "size": 24162,
  "uploadedAt": "2026-10-28T21:05:00+08:00",
  "uploader": "abaguai6689",
  "description": "整理自2027年学霸笔记针灸学，按照经络分类，已移除耳穴章节。"
}
```

字段说明：

| 字段 | 必填 | 说明 |
|---|---|---|
| `file` | 是 | 卡组文件名，必须和实际文件名完全一致 |
| `size` | 是 | 文件字节数，必须和实际文件大小一致 |
| `uploadedAt` | 是 | 上传时间，ISO 8601 格式，建议带时区，例如 `2026-10-28T21:05:00+08:00` |
| `uploader` | 是 | 上传人/GitHub 用户名 |
| `description` | 是 | 文件说明，建议 1～200 字，说明来源、范围、分类方式 |
| `id` | 是 | 文件唯一 ID，更新时保持不变 |
| `version` | 是 | 整数版本号；更新文件时递增，App 用它判断是否需要提示更新 |

`size` 可以用以下命令获取：

```bash
wc -c "卡组文件.md"
# 或
stat -c %s "卡组文件.md"      # Linux
stat -f %z "卡组文件.md"      # macOS
```

## 上传方法

### 方法一：网页上传

1. 在 GitHub 打开本仓库；
2. 进入 `decks/` 下合适的分类目录（没有就新建）；
3. 点 **Add file → Upload files**；
4. 上传卡组文件；
5. 在同目录再上传同名 `.meta.json` 信息文件；
6. 填好提交说明，点 **Commit changes**。

### 方法二：Git 命令

```bash
git clone https://github.com/abaguai6689/suishibeisong-cloud.git
cd suishibeisong-cloud

mkdir -p "decks/针灸与人文"
cp "/path/to/穴位定位.md" "decks/针灸与人文/"
# 创建同目录的 .meta.json，字段见上

git add "decks/针灸与人文"
git commit -m "add: 针灸与人文穴位定位卡组"
git push
```

### 方法三：GitHub API

```bash
# 先把内容转成 base64，再 PUT 到 contents API
base64 -w 0 "穴位定位.md" > /tmp/deck.b64
curl -X PUT \
  -H "Authorization: Bearer <你的 token>" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/abaguai6689/suishibeisong-cloud/contents/decks/针灸与人文/穴位定位.md \
  -d "{\"message\":\"add deck\",\"content\":\"$(cat /tmp/deck.b64)\"}"
```

信息文件同样用 contents API 上传，路径加上 `.meta.json`。

## 格式示例

### Markdown 卡组

```markdown
# 卡组标题（也可以不写 H1）

## 第一章

问题：什么是间隔重复？
答案：在快要忘记时复习，用逐渐拉长的间隔巩固记忆。

## 第二章

问题：...
答案：...
```

推荐使用「问题：/ 答案：」或「Q:/A:」格式；导入时选择「自动」或「按二级标题」。

### ZIP 卡组

```text
我的卡组.zip
├── 我的卡组.md
└── images/
    └── 示意图.png
```

Markdown 中正常写 `![说明](images/示意图.png)`，App 会按文件名匹配。

## 更新与删除

- 更新卡组：覆盖原文件，并同步修改 `.meta.json` 的 `size`、`uploadedAt`、`description`；
- 删除卡组：删除卡组文件和对应 `.meta.json`；
- App 每次打开云端导入都会重新读取仓库，不需要重新发版。

## 说明

- App 使用 GitHub 公开 API 读取目录树，使用 `raw.githubusercontent.com` 下载文件；
- 不需要在 App 里填写 token；
- 请保持目录和文件名稳定，避免用户收藏的卡组失效；
- 如果同一目录有多个卡组，每个卡组都要有自己的 `.meta.json`。
