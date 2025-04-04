# Firewall Settings per node

Bei der Installation deaktivieren - ansonsten siehe: https://docs.redhat.com/en/documentation/openshift_container_platform/4.16/html/installation_configuration/configuring-firewall#network-flow-matrix_configuring-firewall

Am besten das web Template und dann anpassen!

| Name  | Version  | Protokoll  | Ziel-Port   | Aktion  |
|---|---|---|---|---|
| icmp  | ip4  |  tcp |   |  accept |
| ssh  |  ip4 |  tcp | 22  |  accept |
|  openshift | ip4  | tcp  | 80,443,6443  | accept  |
| etcd  | ip4  |  tcp | 2379-2380  | accept  |
| kubernetes  | ip4  | tcp  | 9000-9999,10250-10259,1936  | accept  |
| udp  | ip4  | udp  | 9000-9999,4789,6081  | accept  |
| tcp established  | ip4 | tcp  | 32768-65535  | ack - accept  |