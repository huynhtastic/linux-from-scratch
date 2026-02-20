---
concepts_covered:
  - "[[tar]]"
  - "[[compilation]]"
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
```bash
# Configure is a script that gathers configuration info of the machine, like what tools are available and where they're located or CPU architecture
../configure --prefix=$LFS/tools \
			 # --prefix: tells configure to install the tool in the $LFS/tools directory
			 # --with-sysroot=$LFS: tell build system to look in #LFS for system libs for x-compile
             --with-sysroot=$LFS \
			 # --target=$LFS_TGT: tell configure to x-compile since our $LFS_TGT var is different than`config.guess`
             --target=$LFS_TGT   \
			 # --disable-nls: disables internationalization
             --disable-nls       \
			 # --enable-gprofng=no: disables building gprofng
             --enable-gprofng=no \
			 # --disable-werror: continue building even if there are warnings from host compiler
             --disable-werror    \
			 # --enable-new-dtags:
             --enable-new-dtags  \
             --enable-default-hash-style=gnu
```