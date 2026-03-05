# **S**tream **ed**itor

> A tool to help perform basic text transformations on input streams like files or pipes.

The most common say use will be for text substitutions.
## Basic Syntax
```bash
sed 's/find/replace/' [file.txt]
```

- s: The action flag. s is for `substitution` and d is for `deletion`
- find: the text or pattern you are searching for to apply the action on
- replace: used with the `s` flag, this replaces whatever is specified as `find`
- file.txt: the path of the file to search through. If this is omitted, then it will apply the action to the output of a pipe.
## Address: line filtering
By default, `sed` applies the **command** (`s/find/replace/`) to all lines. Use the **address** to specify which lines you want to apply the command to:

- Line Number: `sed '5s/old/new` only change line 5
- Pattern: `sed '/RTLDLIST=/s/old/new/'` change any line with "RTLDLIST="
## Delimiters: `@`
In [[chapter-5#5.5. Glibc-2.43]], we use `@` as a delimiter because the text we needed to replace had `/`, the default delimiter. We can actually almost anything as a delimiter:
```bash
# These both do the same thing.
sed 's|/usr/bin|/bin'
sed 's@/usr/bin@/bin'
```
## Common Flags
- g: global; replace every instance on the line
- d: delete; `sed '/error/d` deletes every line with 'error'
- -i: in-place; directly edit the file instead of printing to the terminal
- I: case-insensitive; `sed 's/apple/orange/I fruit.txt` or `sed '/error/I d logfile.txt`