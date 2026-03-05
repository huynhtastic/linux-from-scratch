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

## glibc pass 1
Expected SBU: 1.4 (0m37.8588s)
Actual SBU: 10.67 (4m48.715s)

Make:
real    3m40.709s
user    15m44.949s
sys     2m30.436s

Make Install:
real    1m8.006s
user    1m13.962s
sys     0m16.518s
