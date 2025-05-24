# Wireguard Setup

```console
# pacman -S wireguard-tools
```

```console
# cd /etc/wireguard
# (umask 077; touch wg0.conf private_key public_key preshared_key)
# wg genkey > private_key
# wg pubkey > public_key < private_key
# wg genpsk > preshared_key
```

On PeerA:
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

On PeerB:
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
```
