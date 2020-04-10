# Setting up openvpn on CentOS 8

## Install openvpn

``` bash
dnf install epel-release
dnf install openvpn
```

## Get EasyRSA

``` bash
cd /etc/openvpn/
wget https://github.com/OpenVPN/easy-rsa/releases/download/v3.0.7/EasyRSA-unix-v3.0.6.tgz
tar -xf EasyRSA-unix-v3.0.6.tgz
mv EasyRSA-v3.0.6/ easy-rsa/; rm -f EasyRSA-unix-v3.0.6.tgz
```

## Configure EasyRSA variables

``` bash
vim /etc/openvpn/easy-rsa/vars
```

```
set_var EASYRSA                 "$PWD"
set_var EASYRSA_PKI             "$EASYRSA/pki"
set_var EASYRSA_DN              "cn_only"
set_var EASYRSA_REQ_COUNTRY     "BY"
set_var EASYRSA_REQ_PROVINCE    "Vitebsk"
set_var EASYRSA_REQ_CITY        "Vitebsk"
set_var EASYRSA_REQ_ORG         "awesome-company CERTIFICATE AUTHORITY"
set_var EASYRSA_REQ_EMAIL       "your@email.com"
set_var EASYRSA_REQ_OU          "awesome-company EASY CA"
set_var EASYRSA_KEY_SIZE        2048
set_var EASYRSA_ALGO            rsa
set_var EASYRSA_CA_EXPIRE       7500
set_var EASYRSA_CERT_EXPIRE     365
set_var EASYRSA_NS_SUPPORT      "no"
set_var EASYRSA_NS_COMMENT      "awesome-company CERTIFICATE AUTHORITY"
set_var EASYRSA_EXT_DIR         "$EASYRSA/x509-types"
set_var EASYRSA_SSL_CONF        "$EASYRSA/openssl-easyrsa.cnf"
set_var EASYRSA_DIGEST          "sha256"
```

``` bash
chmod +x vars
```

## Generate server keys

``` bash
cd /etc/openvpn/easy-rsa
./easyrsa init-pki
./easyrsa build-ca
./easyrsa gen-req awesome-server nopass
./easyrsa sign-req server awesome-server
# Verify keys
openssl verify -CAfile pki/ca.crt pki/issued/awesome-server.crt
```

## Generate client keys

``` bash
./easyrsa gen-req client01 nopass
./easyrsa sign-req client client01
# Verify client key
openssl verify -CAfile pki/ca.crt pki/issued/client01.crt
```

## Build Diffie-Hellman key

``` bash
./easyrsa gen-dh
```

## Generate Certificate Revoking List key

``` bash
./easyrsa gen-crl
```

## Copy certificate files

### Server keys
``` bash
cp pki/ca.crt /etc/openvpn/server/
cp pki/issued/awesome-server.crt /etc/openvpn/server/
cp pki/private/awesome-server.key /etc/openvpn/server/
```

### Client keys

``` bash
cp pki/ca.crt /etc/openvpn/client/
cp pki/issued/client01.crt /etc/openvpn/client/
cp pki/private/client01.key /etc/openvpn/client/
```
### DH and CRL keys

``` bash
cp pki/dh.pem /etc/openvpn/server/
cp pki/crl.pem /etc/openvpn/server/
```

## Configure server

``` bash
vim /etc/openvpn/server/server.conf
```

``` bash
# OpenVPN Port, Protocol, and the Tun
port 1194
proto udp
dev tun

# OpenVPN Server Certificate - CA, server key and certificate
ca /etc/openvpn/server/ca.crt
cert /etc/openvpn/server/hakase-server.crt
key /etc/openvpn/server/hakase-server.key

#DH and CRL key
dh /etc/openvpn/server/dh.pem
crl-verify /etc/openvpn/server/crl.pem

# Network Configuration - Internal network
# Redirect all Connection through OpenVPN Server
server 10.5.0.0 255.255.255.0
push "redirect-gateway def1" 

push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"

# Enable multiple clients to connect with the same certificate key
duplicate-cn

# TLS Security
cipher AES-256-CBC
tls-version-min 1.2
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-128-CBC-SHA256
auth SHA512
auth-nocache

# Other Configuration
keepalive 20 60
persist-key
persist-tun
compress lz4-v2
push "compress lz4-v2"
daemon
user nobody
group nobody

# OpenVPN Log
log-append /var/log/openvpn.log
verb 3 # Output's verbosity, max = 9
```

## Port forwarding 

``` bash
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
sysctl -p
```

## Firewall configuration

``` bash
# Allow OpenVPN traffic in public and traffic zones
firewall-cmd --permanent --add-service=openvpn
firewall-cmd --permanent --zone=trusted --add-service=openvpn
firewall-cmd --permanent --zone=trusted --add-interface=tun0
# Enable NAT
firewall-cmd --permanent --add-masquerade
SERVERIP=$(ip route get 1.1.1.1 | awk 'NR==1 {print $(NF-2)}')
# --direct --passthrough passes an iptables command through to the firewall.
firewall-cmd --permanent --direct --passthrough ipv4 -t nat -A POSTROUTING -s  10.5.0.0/24 -o $SERVERIP -j MASQUERADE
# Unlike iptables firewall-cmd does require reloading
firewall-cmd --reload
```

## Start OpenVPN service

``` bash
systemctl start openvpn-server@server
systemctl enable openvpn-server@server
```

## Client configuration

``` bash
vim /etc/openvpn/client/client01.ovpn
```

``` bash
client
dev tun
proto udp

# Put here your server's IP
remote xxx.xxx.xxx.xxx 1194

ca ca.crt
cert client01.crt
key client01.key

cipher AES-256-CBC
auth SHA512
auth-nocache
tls-version-min 1.2
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-128-CBC-SHA256

resolv-retry infinite
nobind
persist-key
persist-tun
mute-replay-warnings
verb 3 
```

## Copy client files from server

``` bash
cd /etc/openvpn/
tar -czvf client01.tar.gz client/*
scp root@SERVER_IP:/etc/openvpn/client01.tar.gz .
```

## Connect

``` bash
tar -xzvf client01.tar.gz
cd client
openvpn --config client01.ovpn
```

### Note

Logs will show warnings like 

```
'link-mtu' is used inconsistently, local='link-mtu 1602', remote='link-mtu 1541'
'comp-lzo' is present in local config but missing in remote config, local='comp-lzo'
'cipher' is used inconsistently, local='cipher AES-256-CBC', remote='cipher BF-CBC'
'auth' is used inconsistently, local='auth SHA512', remote='auth SHA1'
'keysize' is used inconsistently, local='keysize 256', remote='keysize 128'

```
According to [this](https://forums.openvpn.net/viewtopic.php?t=26061) link they can be safely ignored

[©](https://www.howtoforge.com/tutorial/how-to-install-openvpn-server-and-client-with-easy-rsa-3-on-centos-8/)