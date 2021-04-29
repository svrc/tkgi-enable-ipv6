# Tanzu Kubernetes Grid Integrated (TKGI) / PKS Enable IPV6

## What does this do?

This enables IPv6 on TKGI worker nodes if your workloads require it.  

NOTE: THIS REQUIRES A REBOOT.   IPV6 support on BOSH stemcells are baked into the kernel but disabled
via GRUB bootloader option, module blacklist, and sysctl disabling.   We re-enable all of those.

Currently we wait for the Kubelet to come up, wait 10 seconds, and reboot.
YOU WILL LIKELY NEED THE BOSH RESURRECTOR DISASBLED DURING ANY BOSH STEMCELL UPGRADES

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your TKGI bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc/tkgi-enable-ipv6 && cd tkgi-enable-ipv6
bosh create-release --force
bosh upload-release ./dev_releases/tkgi-enable-ipv6/tkgi-enable-ipv6-0+dev.1.yml 

```
4. OPTION A:  Configure the addon from this repo for all clusters
```
bosh -n update-config --name=tkgi-enable-ipv6 --type=runtime ./addon.yml
```
5. OPTION B:  Configure the addon from this repo for a specific cluster (see the addon-cluster.yml for details)
```
bosh -n update-config --name=tkgi-enable-ipv6 --type=runtime ./addon-cluster.yml
```
6. Update your TKGI clusters via the Ops Manager "Apply Pending Changes" button with the "Upgrade All Clusters" errand.   You may also use the `tkgi upgrade-cluster` command to apply this addon to any individual cluster.
