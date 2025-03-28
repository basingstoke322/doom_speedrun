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
