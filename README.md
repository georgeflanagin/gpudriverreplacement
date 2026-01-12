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

Prevent other people from logging in while you work.

```bash
touch /etc/nologin
```


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

These commands should modules are still loaded in memory.

```bash
lsmod | grep nvidia
ls /dev/nvidia*
```


Reboot.

```bash
shutdown -r now
```

Let's see what we've got

```
uname -r
```

The development packages are very likely already installed because we had a driver installed.
However, accidents happen, and if the kernel has been updated recently, the wrong versions may
be present. IOW, this step will not hurt anything.

```bash
dnf install -y kernel-devel-$(uname -r) kernel-headers-$(uname -r) gcc make dkms
```

The NVIDIA repo is already enabled or there would be no driver present. Install the
EXACT version you are looking for.

```
dnf install \
  nvidia-driver-580.82.07 \
  nvidia-driver-libs-580.82.07 \
  nvidia-driver-cuda-580.82.07 \
  nvidia-driver-cuda-libs-580.82.07 \
  nvidia-settings-580.82.07 \
  nvidia-persistenced-580.82.07 \
  kmod-nvidia-latest-dkms-580.82.07
```

If DNF complains about missing dependencies, stop and inspect — do not let it pull a newer or better
version to “resolve” things. Now verify:

```bash
dkms status
```

You should see something like this:

```bash
[evan(root):~]: dkms status
nvidia/580.82.07, 6.12.0-124.21.1.el10_1.x86_64, x86_64: installed
```

Once again, reboot.

```bash
shutdown -r now
```

Breathe some life into the computer:

```
systemctl set-default graphical.target
rm /etc/nologin
```

If you want this driver to stay in place and you are completely statisfied with how it works, do this:

```bash
dnf versionlock add nvidia-driver nvidia-driver-libs nvidia-driver-cuda nvidia-driver-cuda-libs kmod-nvidia-latest-dkms
```

