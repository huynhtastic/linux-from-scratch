---
related_chapters:
  - "[[chapter-4]]"
---
# What are Standard Build Units (SBUs)
1 SBU = compilation time of `binutils`.

If `binutils` took 4 mins to compile,  something that will take 4.5 SBUs will be approximately 18 mins (4 mins* 4.5).

This can be affected by host power settings but it's not available for our LFS system until [this chapter in BLFS](https://www.linuxfromscratch.org/blfs/view/systemd/sysutils/power-profiles-daemon.html).

## My SBU
### binutils pass 1
real    0m27.042s
user    0m50.793s
sys     0m13.362s

### gcc pass 1
Expected SBU: 3.8 (1m42.6s)
Actual SBU: 30.07

real    13m33.145s
user    71m3.245s
sys     2m46.226s