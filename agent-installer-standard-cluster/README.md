# Multi Cluster Installation

Boot Server in rescue Mode
pass' auf das kein efibootmgr aktiv ist
wenn dann:
```
sudo efibootmgr -B -b 0005
sudo efibootmgr -n 0001
sudo wipefs --all --force /dev/sdb
```

## DNS Setup
	A	*.apps.agent	88.99.29.202	-	
	A	api-int.agent	88.99.29.202	-	
	A	api.agent	88.99.29.202	-

Loadbalancer Setup vornehmen!    

## Agent-Config
Achte auch die Korrekte IP Config! Hetzner Server und Routen

am besten im rescue system:
```
route -n
```

```
mkdir setup
cp *.yaml setup/
```
update pull config ssh key
```
cd setup/
wget -qO- https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz | gunzip | tar xvf - -C /tmp/
/tmp/openshift-install agent create pxe-files
scp boot-artifacts/* root@server04.muellma.de:www/
```
webserver muss laufen!
Reset wenn nötig Hosts auf Rescue System und dann Hardware Reset
vim /home/marimuel/.ssh/known_hosts

Dann Start Installation:
Firewall am besten disablen auf den Hosts
teste zuvor dass Du alle Hosts per SSH erreichen kannst
```
for i in 5 6 7; do echo "server0$i.muellma.de:";scp ../hetzner-provision-hosts.sh root@server0$i.muellma.de:;ssh root@server0$i.muellma.de ./hetzner-provision-hosts.sh "http://server04.muellma.de:8543/agent.x86_64.ipxe"; done

# Monitoring
/tmp/openshift-install agent wait-for bootstrap-complete
/tmp/openshift-install agent wait-for install-complete
```

## Debug Hints

```
ssh core@server07.muellma.de sudo efibootmgr -n 0001
sudo efibootmgr -B -b 0005
```