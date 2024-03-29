## Linux programs to run/debug pods
- swapoff
- iptables
- mount
- systemd
- socat
- nsenter
- unshare
- ps

## Find pod cgroup
```shell
# Get process ID
ps ax | grep nginx
   7681 ?        SNs    0:00 nginx: master process nginx -g daemon off;
   7722 ?        SN     0:00 nginx: worker process

# Get cgroup path
cat /proc/7681/cgroup 
0::/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-pod0690959e_527a_4618_a4a2_29fce7882e21.slice/cri-containerd-91e5bc91c900ea732b1c65bf15c274e7be860b5740ab0c6578102690139bac3b.scope
```

## Create pod in Linux
### Run process in namespaces
```shell
# Test files
touch /tmp/a /tmp/b /tmp/c

# Directory for isolated box
mkdir -p $HOME/box

# Create directory box
mkdir -p $HOME/box/bin
mkdir -p $HOME/box/lib
mkdir -p $HOME/box/lib64
mkdir -p $HOME/box/proc
mkdir -p $HOME/box/usr/lib
mkdir -p $HOME/box/data

# Copy binary files to box
cp -v /bin/kill $HOME/box/bin
cp -v /bin/ps $HOME/box/bin
cp -v /bin/bash $HOME/box/bin
cp -v /bin/ls $HOME/box/bin

# Copy libraries to box
sudo cp -r /lib/* $HOME/box/lib/
sudo cp -r /lib64/* $HOME/box/lib64/
sudo cp -r /usr/lib/* $HOME/box/usr/lib/

# Mount directories from host to box
sudo mount -t proc proc $HOME/box/proc
sudo mount --bind /tmp/ $HOME/box/data

# Create new namspaces
sudo unshare -p -n -f --mount-proc=$HOME/box/proc

# Create isolated process in new namespaces
chroot ./box /bin/bash

chmod +x ./chroot.sh
./chroot.sh

ls /     
bin  data  lib	lib64  proc  usr

ls /data/
a  b  c

ps
    PID TTY          TIME CMD
      1 ?        00:00:00 sh
      5 ?        00:00:00 bash
      6 ?        00:00:00 ps

ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```
### Create cgroup
v2
```shell
# Add bash in box PID to cgroup and set max memory 10K
mkdir /sys/fs/cgroup/chroot
echo "10K" > /sys/fs/cgroup/chroot/memory.max
echo <PID_bash_in_box> > /sys/fs/cgroup/chroot/cgroup.procs

# Run insude box bash
ls
Killed

```

