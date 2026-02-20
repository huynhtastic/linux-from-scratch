---
related_chapters:
  - "[[chapter-5]]"
---
# **T**ape **Ar**chive
A tool that bundles a bunch of files into a single file.
`tar` only bundles the files and uses other tools for compression.

### Common Flags
-c: create a new archive
-f \[file-name\]: specifies which file to operate on
-x: extract an existing archive
-t: list files inside without extracting
-C: change working directory
### Common Flag Combinations
-xf: extract file; auto detects compression method (zip, cz, bz2)
### Compression Extensions
`.tar.gz`/`.tgz`: gzip (fast)
`.tar.bz2`: bzip2 (small)
`.tar.xz`: XZ (smallest, used by most LFS packages)