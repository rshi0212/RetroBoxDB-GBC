GBC Catalog, storage v4 (64 KiB blocks, 13 solid LZMA2 groups of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- RetroAchievements ROM set imported: 575 ZIPs; 422 ROM files are also in a No-Intro DAT, 145 are only in the RA set, 8 have a hash absent from the latest RA snapshot. RA games with achievements that have a local ROM: 327 → 402.
- Source collections (`source_collections`, `v_collection_files`) and RetroAchievements links per file (`v_ra_collection`).
- ROMs outside every DAT join the family of the stored ROMs they share the most blocks with (hacks next to their original).
- Imports and DAT packaging no longer decode solid groups for already stored blocks; DAT formats (e.g. FDS/QD, NES headered/headerless) are handled separately.
- Source: 3,581 ZIPs (nointro 3,006, retroachievements 575), 1.38 GiB (3,581 ROM files, 4.42 GiB uncompressed). Populated database: 522.2 MiB (37.1% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 2,784 ROM records, 1,576 games, 2,622 releases; DAT versions: 20260602-074724, 20260713-134329, 20260715-062319, 20260814-104253, 20261001-131920.
- RetroAchievements: 402 of 419 games with achievements have a local ROM.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole set in storage order 35.7 MiB/s (3,581 ROM files); single file with a cold cache 1.719 s (ROM) / 1.857 s (TorrentZip) on average.
- Full audit of the populated database: 2,791 objects, 13 groups, 2,931 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-GBC/blob/main/README.zh-CN.md)
