---
related_chapters:
  - "[[chapter-4]]"
---
# What are Standard Build Units (SBUs)
1 SBU = compilation time of `binutils`.

If `binutils` took 4 mins to compile,  something that will take 4.5 SBUs will be approximately 18 mins (4 mins* 4.5).

This can be affected by host power settings but it's not available for our LFS system until [this chapter in BLFS](https://www.linuxfromscratch.org/blfs/view/systemd/sysutils/power-profiles-daemon.html).
