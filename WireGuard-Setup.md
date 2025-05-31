# Wireguard Setup

On each arch system to be connected via wireguard, install the wireguard-tools package.
```console
# pacman -S wireguard-tools
```

On PeerA, change directory into /etc/wireguard and create files for each of wg0.conf, private_key, public_key, and preshared_key. Use the wg command to generate each key.
```console
# cd /etc/wireguard
# (umask 077; touch wg0.conf private_key public_key preshared_key)
# wg genkey > private_key
# wg pubkey > public_key < private_key
# wg genpsk > preshared_key
```
On PeerB, change directory into /etc/wireguard and create files for each of wg0.conf, private_key, and public_key. Use the wg command to generate each key.
```console
# cd /etc/wireguard
# (umask 077; touch wg0.conf private_key public_key)
# wg genkey > private_key
# wg pubkey > public_key < private_key
```
Edit /etc/wireguard/wg0.conf on PeerA and PeerB to include the below configuration, filling in the keys shown. Choose whichever private IPv4 or ULA IPv6 network address you want the devices to be assigned to.

AllowedIPs in the [Peer] sends network traffic to those address to the associated peer rather than your defaul route. For Example:
- AllowedIPs = 10.0.0.1/32
  - Send only traffic destined to 10.0.0.1
- AllowedIPs = 10.0.0.1/24, 192.168.50.0/24
  - Send traffic destined to any address in 10.0.0.1/24 or 192.168.50.0/24
- AllowedIPs = 0.0.0.0/0, ::/0
  - Send all IPv4 and IPv6 address to the peer device

PeerA:
```text
[Interface]
PrivateKey = {Private Key of PeerA}
ListenPort = 51820
Address = 10.0.0.1/24, fd00::1/64

[Peer]
PublicKey = {Public Key of PeerB}
PresharedKey = {Preshared Key of PeerA/PeerB}
AllowedIPs = 10.0.0.2/32, fd00::2/128
```

PeerB: /etc/wireguard/wg0.conf
```text
[Interface]
PrivateKey = {Private Key of PeerB}
ListenPort = 51820
Address = 10.0.0.2/24, fd00::2/64

[Peer]
PublicKey = {Public Key of PeerA}
PresharedKey = {Preshared Key of PeerA/PeerB}
EndPoint = {Public IP address of PeerA}
AllowedIPs = 10.0.0.1/32, fd00::1/128
#Use the below AllowedIPs instead if you want all network traffic to be routed through PeerA
#AllowedIPs = 0.0.0.0/0, ::/0
```
