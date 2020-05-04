# Setting up Exim on CentOS 8

## DNS
### Add DNS zone records for Forward lookup
* Add an A record.

    * Type: A
    * Host: mail
    * Value:

* Add an AAAA record

    * Type: AAAA
    * Host: mail
    * Value:

* Add an SPF record

    * Type: TXT
    * Host: @
    * Value: v=spf1 a a:mail.example.com -all

* Add an MX record

    * Type: MX
    * Host: @
    * Value: mail.example.com. 10

### Set DNS Reverse lookup
[Linode](https://www.linode.com/docs/networking/dns/configure-your-linode-for-reverse-dns/)

## Prepare the machine
### Open mail ports in firewall
``` 
firewall-cmd --add-service=smtp --permanent
firewall-cmd --add-service=smtps --permanent
firewall-cmd --add-service=imap --permanent
firewall-cmd --add-service=imaps --permanent
firewall-cmd --add-service=pop3 --permanent
firewall-cmd --add-service=pop3s --permanent
```

### Set host and domain names
```
# Set the hostname
hostnamectl set-hostname mail.example.com

# Set the domainname
domainname mail.example.com

# Update local hosts file
cat "1.2.3.4 mail.example.com" >> /etc/hosts
```

### Generate SSL certificate
[Let's encrypt](https://certbot.eff.org/lets-encrypt/centosrhel8-other) (Don't forget to disable your firewall, otherwise the generation process is going to fail)

## Exim
### Install exim
```
dnf -y install exim
```

### /etc/exim/exim.conf
Set the full mail server domain name. This needs to match the DNS.
```
primary_hostname = mail.example.com
```
Specify which top level domains we receive mail for 
```
domainlist local_domains = @ : localhost : localhost.localdomain : example.com
```
Specify the SSL certificate
```
tls_certificate = /etc/letsencrypt/live/mail.example.com/fullchain.pem
tls_privatekey  = /etc/letsencrypt/live/mail.example.com/privkey.pem
```
Uncomment this line, so we can use linux account passwords to authenticate mail users 
```
auth_advertise_hosts = ${if eq {$tls_cipher}{}{}{*}}
```
Also comment out this line, to enable the above line:
```
# auth_advertise_hosts = 
```
Let Dovecot do authentification of users:
Fid `authentificatiors` section and add these lines:
```
# Append to authenticators section

dovecot_login:
  driver = dovecot
  public_name = LOGIN
  server_socket = /var/run/dovecot/auth-client
  server_set_id = $auth1

dovecot_plain:
  driver = dovecot
  public_name = PLAIN
  server_socket = /var/run/dovecot/auth-client
  server_set_id = $auth1
```

### Notify linux we're using exim instead of sendmail
```
alternatives --set mta /usr/sbin/sendmail.exim
```

### Test exim config file
```
exim -C /etc/exim/exim.conf -bV
```

### Start exim
```
systemctl start exim
systemctl enable exim
```

## Dovecot
### Install Dovecot
```
dnf -y install dovecot
```

### /etc/dovecot/conf.d/10-ssl.conf
Configure SSL cert for dovecot
```
ssl_cert = </etc/letsencrypt/live/mail.example.com/fullchain.pem
ssl_key = </etc/letsencrypt/live/mail.example.com/privkey.pem
```

### /etc/dovecot/conf.d/10-auth.conf
Allow plaintext logins over ssl
```
disable_plaintext_auth=no
auth_mechanisms = plain login
```

### /etc/dovecot/conf.d/10-mail.conf
Set user mailbox location.
```
mail_location = maildir:~/Maildir
```

### /etc/dovecot/conf.d/10-master.conf
Allow exim to authentificate via dovecot.
```
service auth {
# ...
# ...
    unix_listener auth-client {
        mode = 0660
        user = exim
    }
# ...
# ...
}
```

### Start dovecot
```
systemctl enable dovecot
systemctl start dovecot
```

## User management
### Create a mail account

```
# for me@example.com
adduser -g exim -s /usr/sbin/nologin me
passwd me
```

### Delete a mail account
```
userdel -r me
```

## Spam control
Put following lines into a specified section in `exim.conf`
### acl_check_mail
```
 # If the sender name contains an ip address
  drop message        = Helo name contains a ip address (HELO was $sender_helo_name) and not is valid
       condition      = ${if match{$sender_helo_name}{\N((\d{1,3}[.-]\d{1,3}[.-]\d{1,3}[.-]\d{1,3})|([0-9a-f]{8})|([0-9A-F]$
       condition      = ${if match {${lookup dnsdb{>: defer_never,ptr=$sender_host_address}}}{$sender_helo_name}{no}{yes}}
       delay          = 45s


  # HELO is neither FQDN nor address literal
  drop
    # Required because "[IPv6:<address>]" will have no .s
    condition   = ${if match{$sender_helo_name}{\N^\[\N}{no}{yes}}
    condition   = ${if match{$sender_helo_name}{\N\.\N}{no}{yes}}
    message     = Access denied - Invalid HELO name (See RFC2821 4.1.1.1)

  # HELO is forging my hostname
  drop message   = "REJECTED - Bad HELO - Host impersonating [$sender_helo_name]"
       condition = ${if match{$sender_helo_name}{$primary_hostname}}

  # HELO is faked interface address
  drop condition = ${if eq{[$interface_address]}{$sender_helo_name}}
     message   = $interface_address is _my_ address
```

### acl_check_rcpt
```
# check for sender blacklist
deny message = Access denied - $sender_host_address\
               listed by $dnslist_domain\n$dnslist_text
     dnslists = dnsbl.sorbs.net:sbl.spamhaus.org:bl.spamcop.net:cbl.abuseat.org

# Drop invalid bounces
drop  message = Legitimate bounces are never sent to more than one recipient.
      senders = : postmaster@*
      condition = ${if >{$recipients_count}{0}{true}{false}}
```

[©](https://mitchjacksontech.github.io/Deploy-Dedicated-Mail-Server-with-CentOS7/)