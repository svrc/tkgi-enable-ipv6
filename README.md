# Tanzu Kubernetes Grid Integrated (TKGI) / PKS Worker Custom Cloud Properties

## What does this do?

This will insert custom BOSH cloud properties in the worker VMs by adding to an existing VM extension.

This has only been tested on the vSphere CPI.

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your TKGI bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc/tkgi-worker-cloud-properties && cd tkgi-worker-cloud-properties
bosh create-release --force
bosh upload-release ./dev_releases/tkgi-worker-cloud-properties/tkgi-worker-cloud-properites-0+dev.1.yml 

```
4. Configure the addon from this repo
```
bosh -n update-config --name=tkgi-worker-cloud-properties --type=runtime ./addon.yml
```
5. Update your TKGI clusters via the TKGI CLI and/or Ops Manager "Apply Pending Changes" button with the TKGI upgrade errand enabled.  This addon will automatically be installed on all worker nodes with the default manifest `pks-dpw-manifest.yml`



