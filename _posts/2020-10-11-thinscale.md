---
layout: post
title:  "thinscale: a thinclient setup using tailscale, k3s, rasbpi zeros"
author: "Maaz"
---
# parts requests

- 3 raspberry pi zeros
- [k3s](https://github.com/k3s-io/k3s) - lightweight k8s for IoT/ARM
- ~[wireguard](https://github.com/k8sutils/wireguard-linux)~ [tailscale](https://github.com/k8sutils/tailscale) mesh vpn


![img](https://i.ibb.co/wgG0tjv/32-D726-BA-D6-EB-4-A56-A000-44-E4-A4-CA1-AB6.jpg)


# cluster initialization

 ## download and install
  ```
  curl -sfL https://get.k3s.io | sh -
  # Check for Ready node, 
  takes maybe 30 seconds
  k3s kubectl get node
  ```
## setup nodes

 ```
K3S_TOKEN=SECRET k3s server --server https://masters-up
```
run on each


source: https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/

# plug A into B
* currently tailscale is available as a downloadable binary for Raspbian but we need to containerize it to run on our k8s cluster
* two options
  * dockerize https://github.com/tailscale/tailscale
  * dockerize these steps:*

```
Raspbian Buster (for Raspbery Pi)

# Add Tailscale's GPG key
curl https://pkgs.tailscale.com/unstable/raspbian/buster.gpg | sudo apt-key add -
# Add the tailscale repository
curl https://pkgs.tailscale.com/unstable/raspbian/buster.list | sudo tee /etc/apt/sources.list.d/tailscale.list
# Install Tailscale
sudo apt-get update && sudo apt-get install tailscale
# Start Tailscale!
sudo tailscale up
```

# assemble
- [connect ipad pro to tailscale vpn](https://tailscale.com/kb/1020/install-ios)
- [install blink shell on ipad](https://blink.sh)
- using my own [k8sutils images](https://github.com/k8sutils/bash)


# y tho
- launch k8s pods running task-specific container images with the tooling baked in with your credentials (for github to download your dotfiles, ssh keys, CAs) as k8s secrets to have your shell living as long as you need it, you connect to it as you need
- can switch to laptop also on tailscale network to connect to same pods with same living session and shell history
- if you configure your CNI to a corporate VPN like GlobalProtect the cluster overlay network can be connected to your corporate VPN making the pods themselves be the only things that connect to your VPN, reducing the need for your iPad to be connected to your VPN (if these clusters were run inside the corporation, this would effectively be a zero trust model which is especially important now with WFH)


# notes
- based on http://gist.github.com/maazghani/f605d993d878093c8be3c9decc1b6d4c
- the standard 4096 dhrsa keys were causing high handshake times occasionally, switched to using ecdh keys with letsencrypt CA for smaller but safer keys
- *if this was done for the corporate setup referred to above, the thinclient devices sent to employees could be as minimal as iPads but in order to feasibly distribute a means of asymmetric encryption, the overheard of maintaining tls certificates and a CA would be a nightmare, easier would be to leverage yubikey duo -- https://duo.com/docs/dng*
This post is where you should start.

[site]: https://maaz.io
[blog]:   https://blog.maaz.io
