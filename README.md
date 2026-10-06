# RetroBoxDB GBC

English | [中文说明](README.zh-CN.md)

Single-file SQLite preservation database for Nintendo Game Boy Color. The public Catalog holds metadata only (checksums, DAT and provenance records, header fields, archive recipes and the processing code); it contains no ROM data and cannot restore files. The populated database stays local.

| Item | Value |
| --- | --- |
| Original size | 3,581 source ZIPs, 1.38 GiB (No-Intro 3,006, RetroAchievements sets 575); 3,581 ROM files, 4.42 GiB uncompressed |
| Stored size | populated database 522.6 MiB; public Catalog 45.9 MiB (no ROM data) |
| Ratio | 37.1% of the source ZIPs, 11.5% of the uncompressed ROM files |
| Technology | storage v4: SHA256-deduplicated 64 KiB blocks packed in No-Intro family order into solid LZMA2 groups of up to 256 MiB (256 MiB dictionary); per-block SHA256 and per-object CRC32/MD5/SHA1/SHA256 verification; source ZIPs reproduced byte-for-byte from TorrentZip plans |
| Export performance | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, Python 3.14.4, all checks included. whole newest-DAT set with `export_set.py` (2,503 files, each checked against the DAT hashes): 56.2 MiB/s, 23 ms per file on average; single file with a cold cache (the group is decoded up to the file): ROM 1.719 s, TorrentZip 1.857 s on average |

## Downloads and documents

| File / document | Content |
| --- | --- |
| [RetroBoxDB.GBC.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-GBC/releases/latest/download/RetroBoxDB.GBC.Catalog.sqlite) | Public Catalog (Release asset with `SHA256SUMS`) |
| [Storage v4 guide](RetroBoxDB.Storage-v4.en.md) / [中文](RetroBoxDB.Storage-v4.zh-CN.md) | Storage evaluation, contents, RA, names and maintenance for every platform |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | Storage format, platform adapters, incremental updates, verification |
| [RA list](reports/ra-gbc-games.csv) / [summary](reports/ra-gbc.json), [build report](reports/gbc-build-report.json), [audit resolution](reports/audit-resolution-20261004.md) | Detailed data |

## Storage choice and platform specifics

Change against 32 MiB groups on real data (first 16 family-ordered groups, 487 MiB): 64 MiB −1.04%, 128 MiB −1.56%, 256 MiB −2.65%; 256 MiB chosen by the rule.

- ROMs are 256 KiB–8 MiB, including dual-mode titles that also run on the original Game Boy; the header parser records the CGB mode (enhanced or CGB-only).
- Five local DAT versions are imported and each older one is diffed against the newest. RA games present only in older DATs (for example the Yu-Gi-Oh! Early Days Collection builds) are listed as DAT-only; four RA sets match only No-Intro DB Export files that are not in the DAT (Taiwanese unlicensed dumps).

## Contents

| Item | Value |
| --- | --- |
| ROM records / games / releases | 2,784 / 1,576 / 2,622 |
| DAT coverage per version | 20260602-074724: 2,502/2,604; 20260713-134329: 2,503/2,612; 20260715-062319: 2,503/2,612; 20260814-104253: 2,503/2,614; 20261001-131920: 2,503/2,622 |
| Local ROMs in no DAT | 279 |
| ROM files of the RetroAchievements set | in a No-Intro DAT 422, RA only 145, hash not in the latest RA snapshot 8 ([list](reports/ra-gbc-collection-unknown.csv)); RA games still without a local ROM: [gap list](reports/ra-gbc-missing.csv) |
| No-Intro DB Export + Dump Log 20261001-131920 | 2,678 archives, 2,921 file identities, 2,830 documented hardware assertions; Dump Log Verified 448 |
| RetroAchievements (console 6) | 419 games with achievements: 402 with a local ROM (584 ROMs), 0 with the ROM in a sibling database, 1 DAT only, 0 DB file only, 16 without a No-Intro counterpart |
| Chinese names | 1,763 of 2,124 rows translated (1,156 unique); 1,663 local ROMs have a Chinese name |
| Populated-database audit | 2,791 objects, 13 groups, 2,931 archive plans, all passed |

Every source ZIP is reproduced byte-for-byte from its TorrentZip plan (`v_file_checksums.exported_bytes_equal_source`).

## Usage

```bash
# Query-only audit with the Catalog's embedded engine (also: stats, checksums FILE_ID, help)
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.GBC.Catalog.sqlite audit
# Populated database: export by DAT version, 1G1R, RA achievements, TorrentZip or plain ROMs
python3 -B tools/export_set.py RetroBoxDB.GBC.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# Add new DATs, DB Export / Dump Log snapshots, ROMs and an RA snapshot incrementally
python3 -B tools/update_db.py RetroBoxDB.GBC.sqlite --discover --ra --catalog RetroBoxDB.GBC.Catalog.sqlite
```

Python 3.10+ standard library only. `engine.py` and the other `resources` entries are executable code; run them only from a database you built or a Release asset whose SHA256 you verified. Releases are produced by `.github/workflows/publish-catalog.yml`: it starts from the base Catalog pinned in `release/catalog-release.json`, injects the engine and documents of this commit, checks every data-table digest, runs the tests and the audit, then publishes.
