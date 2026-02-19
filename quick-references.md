## 1.5.1. Things to Mention
https://www.linuxfromscratch.org/lfs/view/13.0-systemd-rc1/chapter01/askforhelp.html
Apart from a brief explanation of the problem being experienced, any request for help should include these essential things:

- The version of the book being used (in this case 13.0-systemd-rc1)
- The host distribution and version being used to create LFS
- The output from the [Host System Requirements](https://www.linuxfromscratch.org/lfs/view/13.0-systemd-rc1/chapter02/hostreqs.html#version-check) script
- The package or section the problem was encountered in
- The exact error message, or a clear description of the problem
- Note whether you have deviated from the book at all

## 2.2 Host System Requirements
https://www.linuxfromscratch.org/lfs/view/13.0-systemd-rc1/chapter02/hostreqs.html
# Pacman
| **Action**                              | **Linux Mint (apt)**    | **Arch (pacman)**      |
| --------------------------------------- | ----------------------- | ---------------------- |
| **Update repo list**                    | `sudo apt update`       | `sudo pacman -Sy`      |
| **Upgrade all packages**                | `sudo apt upgrade`      | `sudo pacman -Su`      |
| **Sync & Update (The "Gold Standard")** | (N/A)                   | `sudo pacman -Syu`     |
| **Install a package**                   | `sudo apt install nvim` | `sudo pacman -S nvim`  |
| **Remove a package**                    | `sudo apt remove nvim`  | `sudo pacman -Rs nvim` |
| **Search for a package**                | `apt search nvim`       | `pacman -Ss nvim`      |
| **Clean cache**                         | `sudo apt clean`        | `sudo pacman -Sc`      |
## 4.6 About the Test Suites
https://www.linuxfromscratch.org/lfs/faq.html#no-ptys
https://www.linuxfromscratch.org/lfs/build-logs/13.0-rc1/