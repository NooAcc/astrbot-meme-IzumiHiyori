# 泉妃爱表情包 / Izumi Hiyori Meme Pack

按 AstrBot 表情包协议（清单模式 `1` + 可选语义扩展 `1` / 元数据模式 `"2.0"`）重构的表情包目录。

## 目录结构

```text
izumi-hiyori-pack/
  manifest.json             清单：99 张图、18 个语义分类，声明了语义扩展
  memes/<category>/*.png    按语义分类归档的图片（保留原始文件名以便溯源）
  previews/cover.png        自动生成的封面
  semantic_metadata.json    99 条逐图语义描述与分类审核状态
  README.md                 本文件
```

分类分布（共 99 张）：

| 分类 | 张数 | 分类 | 张数 | 分类 | 张数 |
| --- | --- | --- | --- | --- | --- |
| happy | 24 | sad | 13 | sigh | 12 |
| surprised | 8 | fool | 7 | like | 7 |
| angry | 5 | baka | 5 | shy | 4 |
| sleep | 3 | confused | 2 | reply | 2 |
| see | 2 | color | 1 | cpu | 1 |
| givemoney | 1 | meow | 1 | please | 1 |

## 使用方式

**本地安装**：把 `izumi-hiyori-pack/` 整个目录放进 `data/plugin_data/meme_manager/packs/`，
或在 WebUI 的「资源广场 / 表情包管理」里按目录导入。

**语义描述**：`semantic_metadata.json` 是插件「分享导出」的格式——包含 caption、标签、图中文字、
分类审核状态与 `text_hash`，但**不含向量、模型名或任何凭据**（文件里带
`"requires_local_index_rebuild": true`）。安装后在语义页面用你自己的向量模型重建索引即可；
不建索引也不影响按分类使用。

## 发布信息

仓库：[NooAcc/astrbot-meme-IzumiHiyori](https://github.com/NooAcc/astrbot-meme-IzumiHiyori)

| 项目 | 值 |
| --- | --- |
| 包 ID | `izumi-hiyori-meme-pack` |
| 版本 | `1.0.0`（对应 tag `v1.0.0`） |
| 作者 | NooAcc |
| 许可 | [CC-BY-NC-SA-4.0](LICENSE) |
| 来源 | `NooAcc/astrbot-meme-IzumiHiyori`，`ref: v1.0.0`，`subpath: .` |

包根目录就是仓库根目录，`manifest.json` 与 `memes/` 同级，因此可以直接通过来源安装：

```text
仓库: NooAcc/astrbot-meme-IzumiHiyori
ref : v1.0.0
子路径: .
```

### 许可说明

本仓库按 CC-BY-NC-SA-4.0 发布：署名、非商业性使用、相同方式共享。素材版权归原作者，
如果你计划商用或再分发，请自行确认授权。完整条款见 [LICENSE](LICENSE)。

### 提交到表情包广场

向 [astrbot-meme-pack-index](https://github.com/anka-afk/astrbot-meme-pack-index) 提 PR，
在 `community-index.json` 追加条目即可（或按 Issue 模板提交，由维护者代加）。

## 维护与重新生成

图片的**唯一来源**是 `meme/curated-memes/<category>/`：目录名就是分类，要改分类直接把图片
拖到对应文件夹。然后执行：

```sh
python meme/build_pack.py
```

脚本会重建 `memes/`、`previews/`、`manifest.json` 与 `semantic_metadata.json`，
自动重算每张图的 SHA-256、`entry_id`、`text_hash` 与 `category_context_hash`。
分类的 caption / 标签 / 图中文字维护在 `meme/build_pack.py` 的 `CLASSIFICATION` 表里
（新增图片没写描述时脚本会报错并列出文件名）。

**不要手工编辑 `semantic_metadata.json` 里的哈希字段**，重新构建即可。

## 质检脚本

只需要一个 OpenAI 兼容端点，凭证通过环境变量传入，不写入仓库：

```sh
export MEME_LLM_BASE_URL=...      # 例如 http://your-host:3000/v1
export MEME_LLM_API_KEY=...

python meme/tools/category_check.py    # 用 embedding+rerank 复核分类与描述是否自洽
python meme/tools/retrieval_eval.py    # 用 43 条真实聊天语境评测检索命中率
```

`retrieval_eval.py` 的 43 条查询覆盖全部 18 个分类，可以直接量化「改一句话到底有没有用」。
当前基线（99 张）：

| 检索方式 | top-1 | top-3 |
| --- | --- | --- |
| 纯 embedding | 62.8% | 76.7% |
| embedding + rerank | **95.3%** | **100%** |

## 说明

- 原始素材共 112 个文件，去掉 2 组字节完全相同与 11 张近似重复后为 99 张。
- 图片保留原来的 `AgAD*.png` 文件名，改名不影响使用（改完重跑构建脚本即可）。
- 语义描述为人工视觉标注：`provenance` 为 `manual`、`category_review_status` 为
  `manual_confirmed`、`manual_override` 为 `true`，插件导入后不会重复调用视觉模型。
