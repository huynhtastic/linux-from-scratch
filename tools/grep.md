# **G**lobal **R**egular **E**xpression **P**rint

> The go-to tool for searching through text.

## Basic Usage
```bash
grep [options] "pattern" [file]
```
- `"pattern"` can be a word like "error" or regex
- `[file]` is the path of the file you want to search. If this is omitted, then it listens for output from a pipe.
## Common Flags
- -i: case-insensitive
- -v: invert match; print everything that doesn't match (vs everything that does)
- -r: recursive; search through every file and subfolder
- -n: line-number; show exactly what line the match was found
## Other flags
- -E: extended regex; removes the requirement of using backslashes for special characters
```bash
	# standard regex
	grep "error\|warning" log.txt
	# extended/modern regex
	grep -E "error|warning"log.txt
```
- -o: only matching; gives only the exact string that matched the pattern rather than the whole line
```bash
grep -o "2\.3\.4" log.txt # gives only 2.3.4
```
- -B#: show # lines before the match
```bash
grep -B3 "/mnt/lib" log.txt # gives 3 lines before and also /mnt/lib
```