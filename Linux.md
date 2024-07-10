# Linux

## Config

### PS1
```
# Terminal PS1
export PS1="\[\033[38;5;39m\][\u@\h \W]> \[$(tput sgr0)\]"

# Remove conda prefix
# save to miniconda3/etc/conda/activate.d/remove_base_ps1.sh
PS1="$(echo "$PS1" | sed 's/(base) //')"
```

## CentOS upgrade

### CentOS 8 to CentOS Stream8

Refers to [link](https://unix.stackexchange.com/questions/552873/how-to-switch-from-centos-8-to-centos-stream)

```bash
# Update installed packages
dnf update

# Install stream release files
dnf install centos-release-stream

# Replace the centos-linux repositories with centos-stream repositories:
dnf swap centos-{linux,stream}-repos

# Upgrade
# if conflict, consider to add --allowerasing
dnf distro-sync

# Check OS version
cat /etc/*release

# Reboot if everything OK
reboot
```

### CentOS Stream 8 to CentOS Stream 9

Refers to [link](https://ahelpme.com/linux/centos-stream-9/how-to-upgrade-to-centos-stream-9-from-centos-stream-8/)

```bash
# Update installed packages
dnf update

# Install release files
dnf install http://mirror.stream.centos.org/9-stream/BaseOS/x86_64/os/Packages/centos-stream-repos-9.0-24.el9.noarch.rpm http://mirror.stream.centos.org/9-stream/BaseOS/x86_64/os/Packages/centos-stream-release-9.0-24.el9.noarch.rpm http://mirror.stream.centos.org/9-stream/BaseOS/x86_64/os/Packages/centos-gpg-keys-9.0-24.el9.noarch.rpm

# Upgrade
# If conflicting request for modules, try dnf module reset xxxx
# If error with iptables-ebtables, try rpm -e iptables-ebtables --nodeps
dnf --releasever=9-stream --allowerasing --setopt=deltarpm=false distro-sync

# Rebuild RPM database
# If cannot open files due to permission error
# try restorecon -Rv /var/lib/rpm
rpmdb --rebuilddb

# Clean DNF cache
dnf clean packages

# Upgrade packages
dnf update

# Install packages for Minimal Install
dnf groupupdate "Core" "Minimal Install"

# Check release version
cat /etc/*release

# Reboot
reboot
```
