# nfs
1. apt install nfs-kernel-server
2. /etc/exports
3. chmod 777 & chmod +t -R
4. /storage/office * (rw,sync,no_subtree_check)
5. exportfs -ra

# pam_mount
1. apt install libpam-mount
2. /etc/security/pam_mount.xml
3. `<volume fstype="nfs" server="ip" path=/storage/office mountpoint="~/Desktop/office" srgrp="office" options="uid=%(USERUID),guid=%(USERGUID),cruid=%(USERUID),file_mode=0644,dir_mode=0755"/>`

# pam_time
commom-auth or fly-dm  
`account requisite pam_time.so`  
time.conf  
`*;*;!admin;Wk0800-1800`  
/etc/X11/fly-dm/fly-dmrc GreetStrimg GreetFont

# reserve  
```#!/bin/bash
while true
 do
   if FN=`inotifywait /home/neo/test --format %w%f -e create,modify -q -r`
   then
    if echo $FN | grep -iq save
    then
        cp $FN /crypto-folder/
    fi
   fi
 done
```
crontab @reboot /script_path

# ca
https://sgolubev.ru/openssl-ca/  
1. mkdir certs keys newcerts db
2. touch db/index.txt
3. echo 1000 > db/serial
4. cp /etc/ssl/openssl.conf .
5. comment engines section
6. edit names
7. edit paths
8. allow copy extensions
9. openssl req -new -x509 -keyout cakey.pem -out cacert.pem -config openssl.conf

https cert
1. openssl req -new -config openssl.conf -keyout keys/key.pem -out csr/csr.pem -nodes -subj "/CN=domain.name/" -addext "subjectAltName=DNS:domain.name"
2. openssl ca -config openssl.conf -in csr/csr.pem -out certs/cert.pem -nobatch

subca cert
1. openssl req -new -config openssl.conf -keyout keys/key.pem -out csr/csr.pem
2. openssl ca -config openssl.conf -in csr/csr.pem -out certs/cert.pem -nobatch -extensions v3_ca

# lvm
apt isntall lvm2 cryptsetup
1. modprobe brd rd_nr=3 rd_size=10485760 max_part=0
2. pvcreate /dev/ram*
3. vgcreate stripe_group dev/ram*
4. lvcreate -i3 -l 100%VG -n data_array stripe_group
5. echo pass | cryptsetup open /dev/stripe_group/data_array crypto-folder --type plain
6. mkfs.ext4 /dev/mapper/crypto-folder
7. mount /dev/mapper/crypto-folder /crypto-folder

# import users
```
import csv
import subprocess

with open('users.csv') as f:
 reader = csv.reader(f, delimiter=";")
 for row in reader:
  subporcess.Popen(f"echo pass | ipa user-add {row[0]} --first={row[1]} --last={row[2]} --password")
```

# freeipa custom cert
ipa-server-certinstall -w -d mysite.key mysite.crt  
The option -w|–http installs the certificate for the HTTP server, and -d|–dirsrv installs the certificate for the LDAP server. Please see ipa-server-certinstall(1) man page for more information regarding all the available options.  

Then restart your daemons:  
systemctl restart httpd.service  
systemctl restart dirsrv@MY-REALM.service 

# syslog
/var/log/zabbix/zabbix_server.log  
/var/log/postgresql/postgresql  

source remote { file("/biba/boba.log"); };  
destination d_net { tcp("192.168.1.1" port(1468) log_file_size(1000)); };  
log { source(remote); destination(d_net); };  

source s_net { tcp(ip(0.0.0.0) port(1468)); };  
destination remote { file("/h/u/i"); };  
log { source(s_net); destination(remote); };  

# zabbix ssl
vi /etc/nginx/conf.d/zabbix.conf  
```
server {
listen 443 http2 ssl;
listen [::]:443 http2 ssl;
server_name <ip qddress>;
ssl_certificate /etc/ssl/certs/zabbix.mycompany.internal.crt;
ssl_certificate_key /etc/pki/tls/private/zabbix.mycompany.internal.key;
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```
To redirect traffic from port 80 to 443 we can add the following lines above our https block:
```
server {
listen 80;
server_name _; #dns or ip is also possible
return 301 https://$host$request_uri;
}
```
systemctl restart php-fpm.service  
systemctl restart nginx  
