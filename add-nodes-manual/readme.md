# Manual add nodes
```
OCP_VERSION=latest-4.18
ARCH=x86_64
oc --kubeconfig agent-installer/setuppxe/auth/kubeconfig extract -n openshift-machine-api secret/worker-user-data-managed --keys=userData --to=- > worker.ign
curl -k https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-install-linux.tar.gz > openshift-install-linux.tar.gz
tar zxvf openshift-install-linux.tar.gz
chmod +x openshift-install
ISO_URL=$(./openshift-install coreos print-stream-json | grep location | grep $ARCH | grep iso | cut -d\" -f4)
curl -L $ISO_URL -o rhcos-live.iso

