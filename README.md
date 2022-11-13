# Announcing hostnames / subdomains in your local network via Avahi & systemd

## prerequisites

- A linux system
  - using **systemd** as their *init system*
  - having **avahi** installed & running
    + install
      - Ubuntu: `sudo apt install libnss-mdns avahi-daemon avahi-utils -y`
      - Fedora: `sudo dnf install nss-mdns avahi avahi-tools -y`
    + run service: `sudo systemctl enable --now avahi-daemon.service`
  - having **git** installed

## all steps can be done by a regular user

### preparations

You need to tell the system that this user is allowed to keep processes running even after the user logged out:

```shell
loginctl enable-linger $(whoami)
```

In order to execute some commands related to systemd as a user, we need to set an environment variable (find out which shell you are using via `echo $0`):

#### bash

```shell
echo "export XDG_RUNTIME_DIR=/run/user/\$(id -u)" >> ~/.bashrc && source ~/.bashrc
```

#### zsh

```shell
echo "export XDG_RUNTIME_DIR=/run/user/\$(id -u)" >> ~/.zshrc && source ~/.zshrc
```


### cloning this project

```shell
git clone https://github.com/mkoester/mDNS_avahi_systemd.git
```

navigate into the repository

```shell
cd mDNS_avahi_systemd/
```

### installing the pod and containers as systemd units

```shell
ln -s $(pwd)/*.service ~/.config/systemd/user/
```

Next we are going to tell systemd about the new units

```shell
systemctl --user daemon-reload
```

(you would need to run this after the unit files changed, i.e. after you ran `git pull`)

## Announcing a hostname / subdomain (IPv4 and IPv6 supported)

```shell
systemctl --user enable --now avahi.publish-host.ipv{4,6}@<hostname>.service
```

```shell
systemctl --user enable --now avahi.publish-subdomain.ipv{4,6}@<subdomain>.service
```

e.g. host `sometesthost` via **IPv4**

```shell
systemctl --user enable --now avahi.publish-host.ipv4@sometesthost.service
```

you should now be able to find this host in your local network

```shell
ping -4 sometesthost
```

## acknowledgements

I got some inspiration from [stackoverflow: ip & sed](https://stackoverflow.com/questions/13322485/how-to-get-the-primary-ip-address-of-the-local-machine-on-linux-and-os-x/49552792#49552792)
