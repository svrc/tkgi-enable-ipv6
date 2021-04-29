# Tanzu Kubernetes Grid Integrated (TKGI) / PKS Enable IPV6

## What does this do?

This enables IPv6 on TKGI worker nodes if your workloads require it.  

NOTE: THIS REQUIRES A REBOOT.   Currently we wait for the Kubelet to come up, wait 10 seconds, and reboot.
YOU WILL LIKELY NEED THE BOSH RESURRECTOR DISASBLED DURING ANY BOSH STEMCELL UPGRADES

This is useful if you churn a lot of Pods, the `cni0` interface MAC addresses will change on every veth creation (pod), which is due to a long standing bug
where Docker was creating this bridge before the CNI plugin, and thereby defaulting to Linux bridge default behavior of ephemeral MAC addresses.   This manifests in ARP cache misses in the Pods fairly often, which can slow high throughput workloads.

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your TKGI bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc/tkgi-disable-docker-bridge && cd tkgi-disable-docker-bridge
bosh create-release --force
bosh upload-release ./dev_releases/tkgi-disable-docker-bridge/tkgi-disable-docker-bridge-0+dev.1.yml 

```
4. Configure the addon from this repo
```
bosh -n update-config --name=tkgi-disable-docker-bridge --type=runtime ./addon.yml
```
5. Update your TKGI clusters via the Ops Manager "Apply Pending Changes" button with the "Upgrade All Clusters" errand.   You may also use the `tkgi upgrade-cluster` command to apply this addon to any individual cluster.
