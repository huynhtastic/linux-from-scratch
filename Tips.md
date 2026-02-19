https://command-not-found.com/

# On Startup
For VBox:
- Mint already grabs the KVM so this needs to be run for VBox to take control of the KVM
- `sudo modprobe -r kvm_intel kvm`