# Raspberry Pi - Kubernetes Cluster

## Changelog

Start from standard _**Hypriot**_ [Raspberry Pi distro](http://blog.hypriot.com/getting-started-with-docker-on-your-arm-device/) (Version [1.7.1](http://blog.hypriot.com/downloads/))

Follow [instructions](http://blog.hypriot.com/getting-started-with-docker-and-mac-on-the-raspberry-pi/) on how to flash OS image to a micro SD card.

#### Update network configuration

Original config:

```
$ more /etc/network/interfaces.d/eth0
allow-hotplug eth0
iface eth0 inet dhcp
```

Change this to:

```
$ more /etc/network/interfaces.d/eth0
allow-hotplug eth0
iface eth0 inet static
    address 192.168.0.101
    netmask 255.255.255.0
    broadcast 10.0.0.255
    gateway 192.168.0.1
```

#### Update the network configuration to not update `/etc/hosts`: comment-out the line containing `update_etc_hosts` in file `/etc/cloud/cloud.cfg`:

```
# The modules that run in the 'init' stage
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - resizefs
 - set_hostname
 - update_hostname
# - update_etc_hosts
 - ca-certs
 - rsyslog
 - users-groups
 - ssh
```

- Set the hostname:

```
$ more /etc/hostname
kubernetes
```

#### Update /etc/hosts to add ip-addresses of other nodes

```
$ more /etc/hosts
127.0.1.1 black-pearl
127.0.0.1 localhost

192.168.0.101 kubernetes
192.168.0.102 node-1
192.168.0.103 node-2
192.168.0.104 node-3
192.168.0.105 node-4

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

#### Repeat process for nodes 1 through 4

#### Notes on differences between this setup and the one in the book

- I decided to use local IP-addresses in a reserved range on my local LAN (192.168.0.X with X >= 100)
- I didn't add IP-forwarding on the kubernetes node, so the instructions to edit `/etc/sysctl.conf` and tuning of `/etc/rc.local` weren't executed.

#### Install Kubernetes

Slightly adapted instructions I used:

Add encryption keys for the packages:

```
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

```
$ sudo vi /etc/apt/sources.list.d/kubernetes.list
$ more /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni
```

Execute the above step on all nodes.

### Intermediate stage backup

Probably a good time to make a backup of what we have so far.

```

```
