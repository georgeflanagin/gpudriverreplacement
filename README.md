# Replacing NVIDIA drivers.

## Assess the current state

Find out what is installed.

```bash
dnf list installed 'nvidia*' 'libnvidia*' 'kmod-nvidia*'
```

Confirm that this is truly what is *running*

```bash
nvidia-smi
```

## Prepare

Stop all the services related to the GUI environment. There are other ways to
do this, but these commands will take effect *now*.

```bash
systemctl isolate multi-user.target
systemctl stop nvidia-persistenced
```

Nuke the old driver.

```bash
dnf remove 'nvidia*' 'libnvidia*' 'kmod-nvidia*'
```
Make sure it is really gone. These commands should verify the absence of the modules.

```bash
lsmod | grep nvidia
ls /dev/nvidia*
```

Reboot.

```
shutdown -r now
```

uname -r

dnf install -y \
  kernel-devel-$(uname -r) \
  kernel-headers-$(uname -r) \
  gcc \
  make \
  dkms

dnf repolist | grep -i nvidia

dnf config-manager --enable nvidia-driver

dnf install \
  nvidia-driver-580.82.07 \
  nvidia-driver-libs-580.82.07 \
  nvidia-driver-cuda-580.82.07 \
  nvidia-driver-cuda-libs-580.82.07 \
  nvidia-settings-580.82.07 \
  nvidia-persistenced-580.82.07 \
  kmod-nvidia-latest-dkms-580.82.07

If DNF complains about missing dependencies, stop and inspect — do not let it pull a newer version to “resolve” things.

dkms status

shutdown -r now

dnf versionlock add \
  nvidia-driver \
  nvidia-driver-libs \
  nvidia-driver-cuda \
  nvidia-driver-cuda-libs \
  kmod-nvidia-latest-dkms


