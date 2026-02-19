---
concepts_covered:
  - "[[standard-build-units]]"
---
# Notes
## 4.6. About the Test Suites
A common issue for `binutils` and `gcc` is running out of pseudo-terminals (PTYs) which most likely means `devpts` FS set up correctly.

Refer to https://www.linuxfromscratch.org/lfs/faq.html#no-ptys for more.

Refer to https://www.linuxfromscratch.org/lfs/build-logs/13.0-rc1/ for expected test failures.
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

## 4.4
```bash
cat > ~/.bash_profile << "EOF"
`exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash`
EOF

# cat > ~/.bash_profile : pipe output of cat into ~/.bash_profile
# << "EOF": A "Here Document" which basically means allow multiple lines of input until you  see the delimiter "EOF" (w/o quotes) in this case
# Basically, write as many lines into ~/.bash_profile until EOF is inputted.

# exec: replace current shell with a new one with the given commands
# env -i: ignore all previous environment variables
# HOME=$HOME TERM=$TERM: re-set the envs HOME and TERM
# PS1='\n:\w\$ ': re-sets the shell prompt
# /bin/bash: run bash
# Basically cleans the shell environment to prevent leakage from the host system (my Arch host VM).

# EOF: delimiter that ends the multiline input into ~/.bash_profile
```

```bash
# Commenting out the cat since it's explained above for better reading
# cat > ~/.bashrc << "EOF"

# Turn off bash's hash function.
# Bash has a hash table to remember paths to executables to save time on repeated executions.
# Turning this off makes bash to search PATH for the newly compiled tools that I guess we're making.
set +h

# Like before, new files + dirs will be writable by owner but readable by anyone.
# Translates to 644 for files and 755 for dirs.
umask 022

# Point to our LFS partition.
LFS=/mnt/lfs

# LC_ALL: controls localization of certain programs
# POSIX == C and ensures functionality in cross-compilation.
LC_ALL=POSIX

# LFS_TGT: deemed the "Target Triplet", a standardized string that tells the compiler exactly what kind of machine it's building for.
# In this case, this outputs to x86_64-lfs-linux-gnu, 4 parts instead of 3.
# The parts are: [CPU Arch]-[Vendor]-[OS/kernel]-[ABI]
# We're using "lfs" as the vendor to trick the build tools to cross-compile, meaning it doesn't use headers/libs from the host (Arch)
# The parallel for flutter is flutter build apk --target-platform android-arm64
#   If I build on a machine with an Intel CPU, this tells the compiler to use ARM instructions instead of Intel ones.
LFS_TGT=$(uname -m)-lfs-linux-gnu

# Follows the convention of modern Linux combining /usr/bin and /bin.
PATH=/usr/bin

# Adds /bin to PATH if it's not a symlink.
if [ ! -L /bin ]; then PATH=/bin:$PATH; fi

# Putting tools in front helps ensure we use the cross-compiler instead of the host's compiler.
PATH=$LFS/tools/bin:$PATH

# Tells configure to look here for system-supported tools and pre-configured settings.
# Helps with consistency, speed, and isolation.
CONFIG_SITE=$LFS/usr/share/config.site

# Export these vars to any sub-shells' environments.
export LFS LC_ALL LFS_TGT PATH CONFIG_SITE
# EOF
```