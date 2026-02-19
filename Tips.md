https://command-not-found.com/

# On Startup
For VBox:
- Mint already grabs the KVM so this needs to be run for VBox to take control of the KVM
- `sudo modprobe -r kvm_intel kvm`

# Changes Made
- Ran `[ ! -e /etc/bash.bashrc ] || mv -v /etc/bash.bashrc /etc/bash.bashrc.NOUSE`
	- Need to undo this move when we're done (not really since I'm using a disposable VM)