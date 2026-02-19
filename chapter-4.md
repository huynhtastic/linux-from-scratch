# Command explanations
## 4.2
```bash
mkdir -pv $LFS/{etc,var} $LFS/usr/{bin,lib,sbin}

# -p: make parent dir if non-existant
# -v: verbose logs
# {etc,var}: a list of dirs to make (didn't know this syntax)

for i in bin lib sbin; do
  ln -sv usr/$i $LFS/$i
done

# Loop over {bin, lib, sbin}
# ln: (hard or soft) link files
#  -s: soft/symbolic link (a shortcut to an existing file)
#      vs. a hard link (a shared ref to the same object in memory)
#  -v: verbose logs
#  usr/$i $LFS/$i: point $LFS/bin -> $LFS/usr/bin and so on
#
# Basically links our /bin, /lib, and /sbin to point to /usr/bin, /usr/lib, and /usr/sbin.
# Some history:
#   1. /bin used to hold essential boot tools and /usr/bin held non-essential tools
#   2. We now just use /usr/bin to hold everything
#   3. Scripts/programs still hard-code #!/bin/bash which will break without the symlink

case $(uname -m) in
  x86_64) mkdir -pv $LFS/lib64 ;;
esac

# Check our architecture and create $LFS/lib64 dir if we're running x86_64-bit
# We need this cause the dynamic loader (the part of the OS that loads libs when a program starts) is hard-coded to look for itself in /lib64/ld-linux-x86-64.so.2.
```

