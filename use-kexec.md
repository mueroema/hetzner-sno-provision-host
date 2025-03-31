# SNO provision from Hetzner rescue

**WARNING**: This is neither supported nor endorsed by Red Hat or Hetzner. It is experimental and not intended for production use. Use it under your own risk.

This is a simple script meant to be run from [Hetzner Rescue System](https://docs.hetzner.com/robot/dedicated-server/troubleshooting/hetzner-rescue-system/) to be able to install OpenShift from the [assisted installer](https://docs.openshift.com/container-platform/4.13/installing/installing_on_prem_assisted/installing-on-prem-assisted.html).

What the script does:
- Installs `kexec-tools` into the rescue system
- It downloads the indicated iPXE script, provided by assisted installer for the discovery phase (as an alternative to the discovery ISO)
- Then it downloads the kernel and the initrd from the URLs on the iPXE script.
- Last, it kexecs into the downloaded kernel and initrd with the kernel parameters provided by the iPXE script

**More warnings**:
- This script assumes that you have previously wiped the hard drives of the node.
- You must feed assisted installer with the right network configuration before generating the iPXE script. Using nmstate file is recommended.
- This worked in the server where I could test it. However, as kexec does not perform the hardware initialization in the very same way than a regular boot, it might not work as expected depending on the server hardware (or just on how lucky you are).

Usage
```
./hetzner-sno-provision-host.sh <iPXE script URL>
```
Where:
- `<iPXE script URL>` is the download URL for the iPXE script provided by assisted installer.

## Verwendung von Agent Installer
### ipxe Files erstellen mit Agent Installer
```
/tmp/openshift-install --dir setuppxe/ agent create pxe-files
```
### Auf den webserver packen und von dort zum download:
```
wget -O agent.x86_64-vmlinuz http://server04.muellma.de:8543/agent.x86_64-vmlinuz
wget -O agent.x86_64-initrd.img http://server04.muellma.de:8543/agent.x86_64-initrd.img
wget -O agent.x86_64-rootfs.img http://server04.muellma.de:8543/agent.x86_64-rootfs.img

```
### kexec installieren - das ändert das Bootmanagement
```
echo kexec-tools kexec-tools/use_grub_config select false | debconf-set-selections
echo kexec-tools kexec-tools/load_kexec select true | debconf-set-selections
apt-get install -y kexec-tools
```

### oder direct in das Rescue System
von dort dann starten:
```
kexec agent.x86_64-vmlinuz --initrd=agent.x86_64-initrd.img --command-line="initrd=initrd coreos.live.rootfs_url=http://server04.muellma.de:8543/agent.x86_64-rootfs.img rw ignition.firstboot ignition.platform.id=metal" 
```

#### Output Monitoring von Installationshost
```
/tmp/openshift-install --dir setuppxe/ agent wait-for bootstrap-complete --log-level=info
INFO Waiting for cluster install to initialize. Sleeping for 30 seconds 
INFO Waiting for cluster install to initialize. Sleeping for 30 seconds 
INFO Cluster is not ready for install. Check validations 
WARNING Cluster validation: The cluster has hosts that are not ready to install. 
WARNING Host master-0 validation: Host couldn't synchronize with any NTP server 
WARNING Host master-0: updated status from discovering to insufficient (Host cannot be installed due to following failing validation(s): Host couldn't synchronize with any NTP server) 
INFO Host master-0 validation: Host NTP is synced 
INFO Host master-0: updated status from insufficient to known (Host is ready to be installed) 
INFO Cluster is ready for install                 
INFO Cluster validation: All hosts in the cluster are ready to install. 
INFO Preparing cluster for installation           
INFO Host master-0: updated status from known to preparing-for-installation (Host finished successfully to prepare for installation) 
INFO Host master-0: New image status quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:c826881b8faa341c109d310d039ccde9369c1e63ff4b0763573589f9a9116268. result: success. time: 9.94 seconds; size: 450.49 Megabytes; download rate: 47.53 MBps 
INFO Host master-0: updated status from preparing-for-installation to preparing-successful (Host finished successfully to prepare for installation) 
INFO Cluster installation in progress             
INFO Host master-0: updated status from preparing-successful to installing (Installation is in progress) 
INFO Host: master-0, reached installation stage Installing: bootstrap 
INFO Host: master-0, reached installation stage Waiting for bootkube 
INFO Bootstrap Kube API Initialized               
INFO Host: master-0, reached installation stage Writing image to disk 
INFO Host: master-0, reached installation stage Writing image to disk: 10% 
INFO Host: master-0, reached installation stage Writing image to disk: 23% 
INFO Host: master-0, reached installation stage Writing image to disk: 30% 
INFO Host: master-0, reached installation stage Writing image to disk: 36% 
INFO Host: master-0, reached installation stage Writing image to disk: 44% 
INFO Host: master-0, reached installation stage Writing image to disk: 58% 
INFO Host: master-0, reached installation stage Writing image to disk: 74% 
INFO Host: master-0, reached installation stage Writing image to disk: 82% 
INFO Host: master-0, reached installation stage Writing image to disk: 89% 
INFO Host: master-0, reached installation stage Writing image to disk: 100% 
INFO Bootstrap configMap status is complete       
INFO Bootstrap is complete                        
INFO cluster bootstrap is complete 

11:16 $ /tmp/openshift-install --dir setuppxe/ agent wait-for install-complete --log-level=info
INFO Bootstrap Kube API Initialized               
INFO Bootstrap configMap status is complete       
INFO Bootstrap is complete                        
INFO cluster bootstrap is complete                
INFO Cluster is installed                         
INFO Install complete!                            
INFO To access the cluster as the system:admin user when using 'oc', run 
INFO     export KUBECONFIG=/home/marimuel/Documents/hetzner-sno-provision-host/agent-installer/setuppxe/auth/kubeconfig 
INFO Access the OpenShift web-console here: https://console-openshift-console.apps.sno.muellma.de 
INFO Login to the console with user: "kubeadmin", and password: "
```