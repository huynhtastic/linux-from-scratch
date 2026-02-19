> Learning about `chmod` because it's such a common permission-related command, but the octal mappings used to scare me a lot. Time to face my fears.

# Activity
Can you decipher this command?
```bash
chmod -v a+wt $LFS/sources
```

If you can, stop reading. You've won the game. Take the rest of the day off. You deserve it.

If you can't, keep reading and join me down the rabbit hole of `chmod`.
## 3.1 Introduction
This chapter in LFS introduces a command to help with permissions in `$LFS/sources`:
```bash
chmod -v a+wt $LFS/sources
```

Back in my day, I only learned to change file permissions using octal codes. Now we have letters?! That sounds so much better! So what does this mean? 

## Linux Permissions Model
First, a basic overview of permissions in Linux.

Thank you https://linuxize.com/post/chmod-command-in-linux/ for this nice overview.

<details>
<summary>My ramblings that are summed up nicely in the table below</summary>
All files (and directories since everything in linux is a file) have 3 things:
- Owner
- Group
- Assigned Permissions

Users are broken down into 3 categories:
- Owner
- Group
- Others (everyone outside owner/group)

Permissions are broken down to **read (r)**, **write (w)**, and **execute (x)**.
</details>

TL;DR:

| **Files Have**  | **Users are categorized as**                                 | **Permissions are categorized as**                 |
| --------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| An owner        | **Owner** of the file                                        | Read (r)                                           |
| A group         | Part of the **Group** that owns the file                     | Write (w)                                          |
| Set permissions | **Others** that aren't the owner or part of the owning group | Execute (x) - running for files, entering for dirs |
## Symbolic Method
So now we're using letters instead of the octals. Gemini has broken this down into a nice table:

| **Part**   | **Character**          | **Meaning**                              |
| ---------- | ---------------------- | ---------------------------------------- |
| **Who**    | `u`, `g`, `o`, **`a`** | **u**ser, **g**roup, **o**thers, **a**ll |
| **Action** | `+`, `-`, `=`          | add, remove, or set exactly              |
| **What**   | `r`, `w`, `x`, `t`     | read, write, execute, sticky bit         |
So using this, we can translate our previous command:
```bash
chmod -v a+wt $LFS/sources

# a: all/everyone
# +: add new permission
# w: write
# t: sticky

# Change $LFS/sources to allow everyone to write new files there and make it sticky aka only owners can delete their own files 
```
You're now ready to use `chmod` for most of your use cases! But if you want to feel cool or like numbers instead of letters and symbols, let's learn the Numeric Method.

## Numeric Method
Basically, permissions are represented as a set of octals in the following order: **owner**, **group**, **others**.

The number of each octal represents the permission of the corresponding user category:
- 0: no permission
- 1: execute
- 2: write
- 4: read

But what about numbers like 3, 5, 6, and 7? Those are the combination of the 3 different permission types:
- 3 (1 + 2): execute + write
- 5 (1 + 4): execute + read
- 6: (2 + 4): write + read
- 7: (1 + 2 + 4): execute + write + read
The number in parenthesis can only be 1, 2, or 4, so you can easily understand what combination of permissions each number represent.

So then something like `744` is:
- 7: owner has read, write, and execute permissions (`rwx`)
- 4: group has read permissions (`r--`)
- 4: others has read permissions (`r--`)

But how about `sticky`? You use a set of 4 octals like `1744`:
- 1: sticky
- 7: owner has read, write, and execute permissions (`rwx`) but can only delete their own files
- 4: group has read permissions (`r--`)
- 4: others has read permissions (`r--`)
### Adding and removing permissions? Nope.
Eagle-eyed readers will notice that we're only setting octals while you can add or remove permissions with symbols.

**Numeric setting is always absolute, as in always set exactly.**
**Symbolic setting can be relative, as in add to or remove from existing permissions, as well as absolute using the = symbol.**

If you want to do relative setting, you have to do some math:
- Check current octal: - `stat -c "%a" <file>`
- Add or remove `1`, `2,` or `4` from the value to get the change you want
- Set the resulting calculation using `chmod` as usual with any octal
### Practice!
Now, we can convert the previous symbolic command into its numeric representation:

```bash
chmod -v a+wt $LFS/sources

# let's say `stat -c "%a" $LFS/sources` returned with 755

# We want to add write permissions to all users (write is 2)
#  7: already has write permissions = 7
#  5: already has read + execute, so add 2 = 7
#  5: same as above = 7
# Intermediate Result: 777
# We also want to make it sticky, so the final result is 1777
```

That's enough to get me through using `chmod` for now! Time to keep reading LFS.