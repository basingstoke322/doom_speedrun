nfs

pam
1. apt install libpam-mount
2. /etc/security/pam_mount.xml
3. `<volume fstype="nfs" server="ip" path=/storage/office mountpoint="~/Desktop/office"/ srgrp="office" options="uid=%(USERUID),guid=%(USERGUID),cruid=%(USERUID),file_mode=0644,dir_mode=0755">`
