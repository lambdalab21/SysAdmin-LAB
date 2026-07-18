#Book/The-Linux-Command-Line Author/Shotts 
#archive
# TLCL Chapter 18: Archiving and Backup — Study Plan

A cleaner phrasing: “Create Markdown files to guide Chapter 18. Use Feynman analogies and disciplined-thinking prompts.”

Recommended split:

| Day | File | Focus |
|---|---|---|
| Day 1 | `01_compression_gzip_bzip2.md` | compression: `gzip`, `gunzip`, `bzip2`, `bunzip2` |
| Day 2 | `02_tar_archives.md` | archive many files with `tar` |
| Day 3 | `03_compressed_tar_archives.md` | `.tar.gz`, `.tgz`, `.tar.bz2` |
| Day 4 | `04_zip_unzip.md` | `zip` and `unzip` |
| Day 5 | `05_rsync_backup.md` | backup/synchronization with `rsync` |
| Day 6 | `06_restore_and_verify.md` | restore practice and safety habits |

Chapter 18 should be learned by restoring files, not merely creating archives.

Core distinction:

```text
compression = make one stream/file smaller
archive     = collect many files into one file
backup      = preserve data in a way you can restore
```

Core rule:

```text
Create → list → restore elsewhere → compare → only then trust.
```
