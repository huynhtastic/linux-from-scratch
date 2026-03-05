---
concepts_covered:
  - "[[tar]]"
  - "[[compilation]]"
  - "[[c-header-files]]"
---
## ii. Toolchain Technical Notes
### Terms
- **The build**: the machine where we build programs, also referred to as the "host"
- **The host**: the machine/system where the built programs will run. Not the same as the host machine in other sections.
- **The target**: the machine the compiler produces code for. May be different from the build and the host.

### The Canadian Cross
Machine A: a slow machine with a compiler (deemed ccA)
Machine B: a fast machine but no compiler
Machine C: a slow machine that's the ultimate target

The book says we build for Machine C by:

| Stage | Build | Host | Target | Action                                          |
| ----- | ----- | ---- | ------ | ----------------------------------------------- |
| 1     | A     | A    | B      | Build cross-compiler cc1 using ccA on machine A |
| 2     | A     | B    | C      | Build cc2 using cc1 on machine A                |
| 3     | B     | C    | C      | Build compiler ccC using cc2 on machine B       |
- 1: Use ccA on A to build cc1
	- cc1 will run on A to build cc2
- 2: Use cc1 on A to build cc2
	- cc2 will run on B to build ccC
- 3: Use cc2 on B to build ccC
	- ccC is used to then build stuff on B for C

This allows us to build anything on fast machine B that targets C.

There could be a 4th step for testing:

| Stage | Build | Host | Target | Action                                       |
| ----- | ----- | ---- | ------ | -------------------------------------------- |
| 4     | C     | C    | C      | Rebuild and test ccC using ccC on machine C. |
Note: ccA and ccC are actually native compilers since they target their hosts.


## 5.2. Binutils-2.46.0 - Pass 1
The tools used to manipulate object files and assembly code.
Contains:
- The Static Linker `ld`
- The Assembler `as`: translates assembly to machine code
- A bunch of other utilities

```bash
# Configure is a script that gathers configuration info of the machine, like what tools are available and where they're located or CPU architecture
../configure --prefix=$LFS/tools \
			 # --prefix: tells configure to install the tool in the $LFS/tools directory
			 # --with-sysroot=$LFS: tell build system to look in $LFS for system libs for x-compile
             --with-sysroot=$LFS \
			 # --target=$LFS_TGT: tell configure to x-compile since our $LFS_TGT var is different than`config.guess`
             --target=$LFS_TGT   \
			 # --disable-nls: disables internationalization
             --disable-nls       \
			 # --enable-gprofng=no: disables building gprofng
             --enable-gprofng=no \
			 # --disable-werror: continue building even if there are warnings from host compiler
             --disable-werror    \
			 # --enable-new-dtags: switch from using rpath to runpath, allowing us to tell LD (Linker/Dynamic) where to look for external deps.
             --enable-new-dtags  \
			 # --enable-default-hash-style=gnu: disables the creation of the ELF hash table which is also created by default
             --enable-default-hash-style=gnu
```
## 5.3. GCC-15.2.0 - Pass 1
Translates C/C++ files into assembly code. Calls Binutils to then finish the rest of the job.
```bash
../configure                  \
    --target=$LFS_TGT         \
    --prefix=$LFS/tools       \
    --with-glibc-version=2.43 \
    --with-sysroot=$LFS       \
	# --with-newlib: ensure inhibit_libc is defined when building to prevent requiring libc
    --with-newlib             \
	# --without-headers: prevent header creation since we don't need it
    --without-headers         \
	# --enable-default-pie, --enable-default-ssp: hardening security features
    --enable-default-pie      \
    --enable-default-ssp      \
    --disable-nls             \
	# --disable-shared: bakes in external dependencies which also disables the creation of the PLT, GOT, and tags
    --disable-shared          \
	# --disable-multilib: prevents creation of 32-bit versions
    --disable-multilib        \
	# --disable-threads, --disable-libatomic, --disable-libgomp, --disable-libquadmath, --disable-libssp, --disable-libvtv, --disable-libstdcxx: prevents compiling unneeded features
    --disable-threads         \
    --disable-libatomic       \
    --disable-libgomp         \
    --disable-libquadmath     \
    --disable-libssp          \
    --disable-libvtv          \
    --disable-libstdcxx       \
	# --enable-languages=c,c++: only build C and C++ compilers
    --enable-languages=c,c++
```

## 5.4. Linux-6.18.10 API Headers
See [[c-header-files]] .

## 5.5. Glibc-2.43
[[standard-build-units#glibc pass 1]]
```bash
../configure                             \
      --prefix=/usr                      \
      --host=$LFS_TGT                    \
      --build=$(../scripts/config.guess) \
	  # --disable-nscd: don't build name service cache daemon (not used)
      --disable-nscd                     \
	  # --libc_cv_slibdir=/usr/lib: don't build at /lib64 (the default for 64-bit machines)
      libc_cv_slibdir=/usr/lib           \
	  # --enable-kernel=5.4: support for 5.4 or later kernels. Disables workarounds for older kernels.
      --enable-kernel=5.4
```

Look at [[sed]]
```bash
sed '/RTLDLIST=/s@/usr@@g' -i $LFS/usr/bin/ldd

# sed: stream editor
# '/RTLDLIST=/': the **address**. "only look for lines that contain 'RTLDLIST='"
# s: substitute command
# @/usr@@: replace "/usr" with nothing. we normally would say `s/usr//`, but use @ as the delimiter because we are looking for '/usr'.
# g: global. without this, sed would only replace the first instance of /usr on this line, just like the global flag for regex.
# -i: in-place. Save changes to file, not just show in terminal.
# $LFS/usr/bin/ldd: the target file path.
```

Look at [[grep]]
```bash
# This command ensures we have sucessfully made these files.
# It will look for:
	# /mnt/lfs/usr/lib/crt1.o succeeded (ctr1.o: contains _start symbol, creating the stack and passes control to the C lib.)
	# /mnt/lfs/usr/lib/crti.o succeeded (ctri.o: initialization code for global contructors that run before `main`.)
	# /mnt/lfs/usr/lib/crtn.o succeeded (ctrn.o: finalization code for global destructors that run after `main`.)
	
grep -E -o "$LFS/lib.*/S?crt[1in].*succeeded" dummy.log

# -E: enable extended regex
# -o: only give the exact matching string, not the entire line
```

```bash
# Makes sure the compiler is searching for the correct header files.
grep -B3 "^ $LFS/usr/include" dummy.log

# -B3: show the 3 lines BEFORE the match instead of just the match.
```

## 5.6. Libstdc++ from GCC-15.2.0
```bash
../libstdc++-v3/configure      \
    --host=$LFS_TGT            \
    --build=$(../config.guess) \
    --prefix=/usr              \
    --disable-multilib         \
    --disable-nls              \
	# --disable-libstdcxx-pch: prevents installation of recompiled include files
    --disable-libstdcxx-pch    \
	# --with-gxx-include-dir=/tools/$LFS_TGT/include/c++/15.2.**0**: 
    --with-gxx-include-dir=/tools/$LFS_TGT/include/c++/15.2.**0**
```