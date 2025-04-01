# Add Nodes
Ab Version 4.17 kannst Du mit oc ein node iso erstellen:

Am besten mit einer nodes-config.yaml um die Network Config zu setzen. 

```
oc --kubeconfig ../agent-installer/setuppxe/auth/kubeconfig adm node-image create -o node.x86_64.iso
```

## iso mounten:
```
sudo mount -o loop node.x86_64.iso /mnt
sudo tree /mnt/
/mnt/
├── coreos
│   ├── features.jso
│   ├── igninfo.jso
│   ├── kargs.jso
│   └── miniso.dat
├── EFI
│   └── redhat
│       └── grub.cfg
├── images
│   ├── efiboot.img
│   ├── ignition.img
│   └── pxeboot
│       ├── initrd.img
│       ├── rootfs.img
│       └── vmlinuz
├── isolinux
│   ├── boot.cat
│   ├── boot.cat
│   ├── boot.msg
│   ├── isolinux.bin
│   ├── isolinux.cfg
│   ├── ldlinux.c32
│   ├── libcom32.c32
│   ├── libutil.c32
│   └── vesamenu.c32
└── zipl.prm
mkdir pxefiles
sudo cp /mnt/images/pxeboot/rootfs.img pxefiles
sudo cp /mnt/images/pxeboot/initrd.img pxefiles
sudo cp /mnt/images/ignition.img pxefiles
sudo umount /mnt
```

## iso Datei Struktur
### Wichtige Dateien und ihre Funktionen

|Datei/Ordner|Funktion|
-----------------------
|isolinux/isolinux.bin|Bootloader für BIOS|
|EFI/redhat/grub.cfg|GRUB 2 Konfigurationsdatei für UEFI|
|images/efiboot.img|Enthält den UEFI-Bootloader|
|images/pxeboot/vmlinuz|Kernel-Datei für den Start|
|images/pxeboot/initrd.img|Initial Ramdisk für den Bootprozess|

### Fazit
Wenn das System im BIOS-Modus bootet, wird ISOLINUX aus isolinux/ geladen.
Wenn das System im UEFI-Modus bootet, wird GRUB 2 aus EFI/redhat/ verwendet.

### verschiedene Versuche das über kexec zu starten schlugen fehl:

```
kexec vmlinuz --initrd=initrd.img --initrd=ignition.img --command-line="rw coreos.liveiso=rhcos-418.94.202501221327-0 ignition.firstboot ignition.platform.id=metal"
```
Das ignition.img enthält die relevante Config, schau mal da:

```
#gz ranhängen
gzip -d -k /tmp/ignition.img.gz
# steuerzeihen raus
cat /tmp/ignition.img | jq .
{
  "ignition": {
    "config": {
      "replace": {
        "verification": {}
      }
    },
    "proxy": {},
    "security": {
      "tls": {}
    },
    "timeouts": {},
    "version": "3.2.0"
  },
  "passwd": {
    "users": [
      {
        "name": "core",
        "passwordHash": "$2a$10$7ThUllpuzO0MdjKRo81fluxHm7NZ6WDESBEoQ9ZS8dgNvG9ZYU0iO",
        "sshAuthorizedKeys": [
          "ssh-rsa AAAAB3NzaC1yc2EAAAADA.....HetznerServerKey"
        ]
      }
    ]
  },
  "storage": {
    "directories": [
      {
        "group": {},
        "overwrite": true,
        "path": "/etc/assisted/clusterconfig",
        "user": {
          "name": "root"
        },
        "mode": 420

```