### Boote das Rescue Image
schaue auch in den efibootmgr, es muss die Netzwerkkarte booten, damit das RescueImage angezogen wird. Die OCP Installtion installiert den Red Hat Boot Manager
```
sudo efibootmgr -n 0001
```
### im rescue Image
Du musst den cargo installieren und von dort coreos-installer - cargo direct, nicht mit apt - ist zu alt!
Schau' das Du alle Partitions los wirst, damit die Platten leer sind

#### ein LVM einrichten - geht nicht für den coreos-installer!!
```
wipefs --all --force /dev/sda
wipefs --all --force /dev/sdb
pvcreate /dev/sda /dev/sdb
vgcreate vg_system /dev/sda /dev/sdb
lvcreate -L 32G -n lv_swap vg_system
lvcreate -l 100%FREE -n lv_root vg_system
mkfs.ext4 /dev/vg_system/lv_root
mkswap /dev/vg_system/lv_swap
lsblk
```

#### coreos-installer installieren
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
apt install -y libzstd-dev libssl-dev
cargo install coreos-installer
```
### From Assist Installer 
hole das iso dann kannst Du installieren
```
coreos-installer install --insecure -f discovery_image_sno.iso /dev/sda
```

