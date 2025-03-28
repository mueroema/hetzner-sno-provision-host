# Assisted Installer 

Du kannst entweder das hetzner-sno-provision-host.sh Script verwenden - was kexec nutzt und die hetzner maschine in den Discovery Mode bootet - sodass die Maschine im Assisted Installer für die Clusterinstallation erscheint.

Alternativ kannst Du auch das ISO aus dem Assisted Installer nehmen und die Maschine damit booten - Wie? siehe Anleitung hier. rescuesystem-use-full-discovery-iso-image.md

-------------------

# Agent Installer

wget -qO- https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz | gunzip | tar xvf - -C /tmp/
sudo dnf install /usr/bin/nmstatectl -y

mkdir agent-installer/setup
# ... sonst werden sie gelöscht
cp agent-installer/*.yaml agent-installer/setup/
/tmp/openshift-install --dir agent-installer/setup/ agent create image