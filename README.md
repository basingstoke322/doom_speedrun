# nfs
1. apt install nfs-kernel-server
2. /etc/exports
3. chmod 777 & chmod +t -R
4. /storage/office * (rw,sync,no_subtree_check)
5. exportfs -ra

# pam
1. apt install libpam-mount
2. /etc/security/pam_mount.xml
3. `<volume fstype="nfs" server="ip" path=/storage/office mountpoint="~/Desktop/office" srgrp="office" options="uid=%(USERUID),guid=%(USERGUID),cruid=%(USERUID),file_mode=0644,dir_mode=0755"/>`

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

# ca
1. mkdir certs keys newcerts db
2. touch db/index.txt
3. echo 1000 > db/serial
4. cp /etc/ssl/openssl.conf .
5. comment engines section
6. edit names
7. edit paths
8. openssl req -new -x509 -keyout cakey.pem -out cacert.pem -config openssl.conf
