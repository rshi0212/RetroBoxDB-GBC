GBC Catalog, storage v4 (64 KiB blocks, 13 solid LZMA2 groups of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- One schema for all twenty-three platforms: the header tables of the eight new platforms (Game Gear, PC Engine, SuperGrafx, MSX, MSX2, Virtual Boy, Game & Watch, Super A'Can: `pce_hardware`, `msx_hardware`, `vb_hardware`) and `rom_annotations` exist in every Catalog; tables of other platforms and provider tables have no rows.
- RetroAchievements reports look up sibling databases (NES<->FDS, SNES<->Satellaview, WonderSwan<->WonderSwan Color, NeoGeo Pocket<->NeoGeo Pocket Color, PC Engine<->SuperGrafx, MSX<->MSX2): a game whose ROM is stored there is `local_other_platform`, not a gap.
- Source: 3,581 ZIPs (nointro 3,006, retroachievements 575), 1.38 GiB (3,581 ROM files, 4.42 GiB uncompressed). Populated database: 522.6 MiB (37.1% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 2,784 ROM records, 1,576 games, 2,622 releases; DAT versions: 20260602-074724, 20260713-134329, 20260715-062319, 20260814-104253, 20261001-131920.
- RetroAchievements: 402 of 419 games with achievements have a local ROM.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole newest-DAT set with export_set.py 56.2 MiB/s (2,503 files); single file with a cold cache 1.719 s (ROM) / 1.857 s (TorrentZip) on average.
- Full audit of the populated database: 2,791 objects, 13 groups, 2,931 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-GBC/blob/main/README.zh-CN.md)
