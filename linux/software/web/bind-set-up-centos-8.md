# Setting up BIND on CentOS 8

## Install BIND

``` bash
dnf install bind bind-utils
```

## Configure BIND

``` bash
vim /etc/named.conf
```

Add following lines into `options` section:


```
options {
        listen-on port 53 { 127.0.0.1; YOUR_SERVER_IP; };
        allow-query     { any; };
};
```

Add this line at the end of the file

```
include "/etc/named/named.conf.local";
```

## Configure local file

``` bash 
vim /etc/named/named.conf.local
```

Add following lines

```
zone "your.domain" {
        type master;
        file "/etc/named/zones/db.your.domain";
};

// ip should be written backwards, e.g. 127.0.0.1 becomes 1.0.0.127.in-addr.arpa
zone "YOUR_REVERSED_IP.in-addr.arpa" { 
        type master;
        file "/etc/named/zones/db.YOUR_IP";
};
```

## Create forward zone file

``` bash 
chmod 755 /etc/named
mkdir /etc/named/zones
vim /etc/named/zones/db.YOUR_DOMAIN
```
Add following lines

```
@       IN      SOA     ns1.YOUR_DOMAIN. admin.YOUR_DOMAIN. (
                              3         ; Serial
             604800     ; Refresh
              86400     ; Retry
            2419200     ; Expire
             604800 )   ; Negative Cache TTL

; name servers - NS records
    IN      NS      ns1.YOUR_DOMAIN.

; name servers - A records
;YOUR_NAME_SERVER_IP can be the same as YOUR_SERVER_IP
ns1.YOUR_DOMAIN.          IN      A       YOUR_NAME_SERVER_IP 

YOUR_DOMAIN.          IN      A       YOUR_SERVER_IP
```

## Create reverse zone file

``` bash
vim /etc/named/zones/df.YOUR_IP
```

Add following lines
```
@       IN      SOA     ns1.YOUR_DOMAIN. admin.YOUR_DOMAIN. (
                              3         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; name servers - NS records
      IN      NS      ns1.YOUR_DOMAIN.

; PTR Records
YOUR_IP   IN      PTR     ns1.YOUR_DOMAIN.
YOUR_IP   IN      PTR     YOUR_DOMAIN.
```

## Test your configuration

``` 
named-checkconf
named-checkzone YOUR_DOMAIN /etc/named/zones/db.YOUR_DOMAIN
named-checkzone YOUR_REVERSED_IP.in-addr.arpa /etc/named/zones/db.YOUR_IP
```


## Start BIND
``` bash
systemctl enable named
systemctl start named
```
[©](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7#configure-dns-clients)