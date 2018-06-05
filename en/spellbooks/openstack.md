Title: conjure-up | Spellbooks | OpenStack
table_of_contents: True

# OpenStack Spell

## Steps

### 00_process-providertype

Provider specific alterations

#### Phases

**before-deploy** - If OpenStack is being deployed on a Localhost (LXD)
provider, some modifications to LXD profiles are required, this phase handles
those modifications.

#### Options

No configuration options for this step.

### 01_glance

Installs glance images

#### Phases

**after-deploy** - Downloads and imports images into glance for use in your OpenStack deployment.

#### Options

No configuration options for this step.

### 02_keypair

Imports ssh keypair for authenticating with your compute nodes.

#### Phases

**after-deploy** - Imports existing or newly generated ssh keypairs into your OpenStack.

#### Options

**sshpublickey** - Path to an existing ssh keypair

#### Example Conjurefile

```yaml
spell: openstack-novalxd
steps:
  02_keypair:
    sshpublickey: ~/.ssh/openstack_rsa.pub
```

### 03_neutron

Configures neutron networking

#### Phases

**after-deploy** - Configures neutron external/tenant networking and updates security groups to allow ssh

#### Options

No configuration options for this step.

### 04_horizon

Installs Horizon admin dashboard

#### Phases

**after-deploy** - Queries the cluster and provides the horizon dashboard ip
address and login credentials and displays that to the user.

#### Options

No configuration options for this step.

## Addons

No addons available.

## Limitations

### Deployment gets part way through and seems to hang under VSphere

The most common cause of a "hung" deployment running inside VSphere is network
related. The most common solution is to enable promiscuous mode in your virtual
switch, Please see [the VMware KB article][vmwarekb] on enabling that feature.

### config-changed error for neutron-gateway

This error can happen if you have not set your **bridge-mappings** and
**data-port** during the [Application List][applist] portion of the deployment.

Please see [the Neutron Gateway charm documentation][neutron] under **Port
Configuration** for more information.

<!-- LINKS -->
[hardware]: ./index.md#hardware-requirements
[vmwarekb]: https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1004099
[applist]: ./index.md#application-list
[neutron]: https://jujucharms.com/neutron-gateway/
