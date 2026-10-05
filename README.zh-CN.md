# RetroBoxDB GBC

[English](README.md) | 中文

任天堂 Game Boy Color的单文件 SQLite 保存库。公开的 Catalog 只含元数据（校验值、DAT 与来源记录、头部字段、打包配方和程序），不含 ROM 数据，不能独立恢复文件；完整库保留在本地。

| 项目 | 数值 |
| --- | --- |
| 原始大小 | 源 ZIP 3,581 个，1.38 GiB（No-Intro 3,006 个，RetroAchievements 集合 575 个）；解压后 ROM 3,581 个，4.42 GiB |
| 入库后大小 | 完整库 522.2 MiB；公开 Catalog 45.5 MiB（不含 ROM 数据） |
| 比例 | 完整库为原 ZIP 的 37.1%，为解压后 ROM 总量的 11.5% |
| 使用的技术 | 存储 v4：64 KiB 块按 SHA256 去重，按 No-Intro 游戏族顺序装入最大 256 MiB 的 LZMA2 实体组（字典 256 MiB）；逐块 SHA256、逐对象 CRC32／MD5／SHA1／SHA256 校验；源 ZIP 由 TorrentZip 配方逐字节重建 |
| 导出性能 | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz，空闲负载，Python 3.14.4，含全部校验。按最新 DAT 整套导出（`export_set.py`，2,503 个文件，逐个按 DAT 哈希校验）：56.2 MiB/s，平均 23 毫秒／个；单个文件冷缓存（每次清空缓存，需解压所在组的前段）：ROM 平均 1.719 秒，TorrentZip 平均 1.857 秒 |

## 下载与说明

| 文件／文档 | 内容 |
| --- | --- |
| [RetroBoxDB.GBC.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-GBC/releases/latest/download/RetroBoxDB.GBC.Catalog.sqlite) | 公开 Catalog（Release 附件，附 `SHA256SUMS`） |
| [存储 v4 说明](RetroBoxDB.Storage-v4.zh-CN.md)／[English](RetroBoxDB.Storage-v4.en.md) | 七个平台的存储评估、内容、RA、中文名与维护 |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | 存储格式、平台适配、增量更新、校验 |
| [RA 清单](reports/ra-gbc-games.csv)／[汇总](reports/ra-gbc.json)、[构建报告](reports/gbc-build-report.json)、[审计处理](reports/audit-resolution-20261004.md) | 逐项数据 |

## 本平台的存储选择与特殊情况

真实全量数据（按族排序的前 16 个组，487 MiB）上相对 32 MiB 组的变化：64 MiB −1.04%，128 MiB −1.56%，256 MiB −2.65%；按规则采用 256 MiB。

- ROM 为 256 KiB–8 MiB，包括也能在初代 Game Boy 上运行的双模式游戏；头部解析记录 CGB 模式（增强或仅 CGB）。
- 导入本地 5 版 DAT，每个旧版本都与最新版做差异。只出现在旧版 DAT 中的 RA 游戏（如 Yu-Gi-Oh! Early Days Collection 的版本）列为“仅 DAT 有”；另有 4 个 RA 套装只对应 No-Intro DB Export 中不在 DAT 里的文件（台湾无授权版本）。

## 内容

| 项目 | 数值 |
| --- | --- |
| ROM 记录／游戏组／发行版本 | 2,784／1,576／2,622 |
| 各版 DAT 覆盖 | 20260602-074724：2,502/2,604；20260713-134329：2,503/2,612；20260715-062319：2,503/2,612；20260814-104253：2,503/2,614；20261001-131920：2,503/2,622 |
| 不在任何 DAT 的本地 ROM | 279 |
| RetroAchievements 集合中的 ROM 文件 | DAT 中有 422，仅 RA 收录 145，哈希不在最新 RA 快照 8（[清单](reports/ra-gbc-collection-unknown.csv)）；仍缺本地 ROM 的 RA 游戏见 [缺口清单](reports/ra-gbc-missing.csv) |
| No-Intro DB Export＋Dump Log 20261001-131920 | 2,678 个档案、2,921 个文件身份、2,830 条有文档的硬件声明；Dump Log Verified 448 |
| RetroAchievements（console 6） | 有成就的游戏 419 个：本地有 ROM 402（584 个 ROM），仅 DAT 有 1，仅 DB 文件 0，无 No-Intro 对应 16 |
| 中文名 | 2,124 条记录中 1,763 条有中文（1,156 个唯一名）；本地 ROM 1,663 个有中文名 |
| 完整库审计 | 2,791 个对象、13 个组、2,931 个 ZIP 配方，全部通过 |

源 ZIP 均可由 TorrentZip 配方逐字节重建（`v_file_checksums.exported_bytes_equal_source`）。

## 使用

```bash
# 用 Catalog 内嵌引擎做只读审计（stats、checksums FILE_ID、help 同理）
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.GBC.Catalog.sqlite audit
# 完整库：按 DAT 版本、1G1R、RA 成就、TorrentZip／裸 ROM 组合导出
python3 -B tools/export_set.py RetroBoxDB.GBC.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# 增量加入新 DAT、DB Export／Dump Log、ROM 与 RA 快照
python3 -B tools/update_db.py RetroBoxDB.GBC.sqlite --discover --ra --catalog RetroBoxDB.GBC.Catalog.sqlite
```

只需 Python 3.10+ 标准库。`resources` 中的 `engine.py` 等是可执行代码，只应从自己构建或 SHA256 已核对的 Release 附件中执行。发布由 `.github/workflows/publish-catalog.yml` 完成：工作流从 `release/catalog-release.json` 固定的基础 Catalog 出发，注入本仓库提交中的引擎与文档，核对全部数据表摘要、运行测试与审计后发布。
