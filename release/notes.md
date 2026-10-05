GBC Catalog, storage v4 (64 KiB blocks, 10 solid LZMA2 groups of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- First release.
- Source: 3,006 No-Intro ZIPs, 1.06 GiB (3,006 ROM files, 3.50 GiB uncompressed). Populated database: 504.1 MiB (46.6% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 2,605 ROM records, 1,576 games, 2,622 releases; DAT versions: 20260602-074724, 20260713-134329, 20260715-062319, 20260814-104253, 20261001-131920.
- RetroAchievements: 327 of 419 games with achievements have a local ROM.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole set in storage order 26.5 MiB/s (3,006 ROM files); single file with a cold cache 1.676 s (ROM) / 1.923 s (TorrentZip) on average.
- Full audit of the populated database: 2,612 objects, 10 groups, 2,679 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-GBC/blob/main/README.zh-CN.md)
