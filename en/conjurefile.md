Title: conjure-up | Conjurefile
table_of_contents: True

# Conjurefile

**conjure-up** can be run in a fully automated fashion, allowing you to
pre-define all required input information for the spell being deployed.

You can always see the latest supported options for a Conjurefile by running

```bash
conjure-up --gen-config
```

This will print out documented YAML that can be redirected to a file for future use.

## Running

If the **Conjurefile** is in the same directory where **conjure-up** is
executed, that config will be automatically loaded and applied to the
deployment. Otherwise passing `-c` to `conjure-up` will allow you to specify the
location of the **Conjurefile**.

```bash
conjure-up -c /home/user/deployments/k8s/Conjurefile
```

## Most used options

Some of the most used options are highlighted below.

**spell** - Provide the name of a spell in the spells registry, a spell in a
remote git repository, or a spell on your local filesystem.

```yaml
# Core Spell
spell: canonical-kubernetes

# Github
spell: battlemidget/ghost

# Local Filesystem
spell: ~/spells/hadoop-spark
```

**registry** - Custom location of a spells repository

```yaml
registry: https://github.com/conjure-up/spells.git
```

**spells-dir** - A local directory of spells. **Note**: Must be used with `no-sync` option if you do not want to override spells in that directory.

```yaml
spells-dir: /home/user/spells
```

**no-sync** - Do not sync a local spells-dir with a remote registry

```yaml
no-sync: true
```

**cloud** - The cloud to use. This can be any cloud listed at [Public Clouds](https://jujucharms.com/docs/stable/clouds#listing-available-clouds)

```yaml
# LXD
cloud: localhost

# AWS East region
cloud: aws/us-east-1
```

**controller** - Controller name. This can be any arbitrary name of your Juju controller.

```yaml
controller: us-datacenter-1
```

**model** - Model name. This can be any arbitrary name of your Juju model

```yaml
model: k8s-staging-1a
```

**model-config** - Options to set on a controller model, see [Model Options](https://docs.jujucharms.com/devel/en/models-config#list-of-model-keys)

```yaml
model-config:
    vpc-id: VPC1234
    apt-mirror: http://us.acme.corp.com/ubuntu/
```

**apt-proxy/apt-https-proxy** - APT Proxy

```yaml
apt-proxy: http://localhost:5555
apt-https-proxy: https://localhost:5556
```

**http-proxy/https-proxy** - HTTP/S Proxy

```yaml
http-proxy: http://localhost:4444
https-proxy: https://localhost:4445
```

**no-proxy** - Comma separate list of ips to not filter through a proxy

```yaml
no-proxy: 8.8.8.8,172.16.0.1
```

**bundle-add** - A bundle fragment to overlay existing spells bundle.

For example, if you want to setup a Canal networking stack for your Kubernetes
cluster, a bundle fragment would look like:

```yaml
services:
  canal:
    annotations:
      gui-x: '450'
      gui-y: '750'
    charm: cs:~containers/canal
relations:
- - canal:etcd
  - etcd:db
- - canal:cni
  - kubernetes-master:cni
- - canal:cni
  - kubernetes-worker:cni
```

Saving this file to `/home/user/canal-bundle-fragment.yaml`, you could then have this be applied by setting the `bundle-add` to:

```yaml
bundle-add: /home/user/canal-bundle-fragment.yaml
```

**bundle-remove** - A bundle fragment to remove from an existing spell bundle.

For example, if you want to remove Flannel from the Kubernetes spell, it would look like:

```yaml
services:
  flannel:
    annotations:
      gui-x: '450'
      gui-y: '750'
    charm: cs:~containers/flannel
relations:
- - flannel:etcd
  - etcd:db
- - flannel:cni
  - kubernetes-master:cni
- - flannel:cni
  - kubernetes-worker:cni
```

Saving this file to `/home/usr/remove-flannel-service.yaml`, and then have this
applied by setting `bundle-remove` to that filename:

```yaml
bundle-remove: /home/user/remove-flannel-service.yaml
```

## Customising Steps

Each spell come with steps that are ran after a deployment is complete. They
could be anything from displaying the Horizon dashboard URL in an OpenStack
deployment, to creating storage classes in a Kubernetes cluster.

In some cases, steps require user input and a `Conjurefile` can facilitate having those questions answered. The basic format for defining a step in a `Conjurefile` is:

```yaml
spell: canonical-kubernetes

steps:
  01_select-network:
    networkplugin: "flannel"
```

This example defines the network stack used in the `canonical-kubernetes` spell. To better understand how to define the key/values you will have to look at how the spell
defines its steps. In this case, the directory structure of the spell is:

```
canonical-kubernetes/
    steps/
       01_select-network/
          metadata.yaml
```

To configure the `01_select-network` step, the key `01_select_network` is used
and within that directory holds the `metadata.yaml` file. This file is where you
will find the available options to be defined in the `Conjurefile`.

Looking at the `metadata.yaml` file you will see:

```yaml
title: Choose Kubernetes Network Plugin
description: Choose the type of network plugin you wish to use for your cluster
viewable: True
additional-input:
  - label: Network Plugin
    key: NETWORKPLUGIN
    type: choice
    default: flannel
    choices:
      - flannel
      - calico
```

If you look at the `key: NETWORKPLUGIN` and the `choices`, you can then set which network plugin to use during deployment (flannel or calico).

!!! Note:
    All keys in a `Conjurefile` are lowercase by default and uppercase in the steps `metadata.yaml`.

To find out what steps and options can be defined for a particular spell, see the [Spellbook][spellbook]

## Customising Addons

Just like in **Customising Spells** you can predefine addons and their values. The directory structure is outlined as:

```
canonical-kubernetes/
    addons/
      helm/
        steps/
          01_install-helm/
            metadata.yaml
```

The `metadata.yaml` for this addon is:

```yaml
title: Install Helm
description: |
  Download and installs Helm
viewable: True
additional-input:
  - label: Helm Version
    key: HELM_VERSION
    type: text
    default: v2.8.1
```

To set a different version of **Helm** you would define your `Conjurefile` with:

```yaml
addons:
  helm:
    01_install-helm:
        helm_version: v2.8.1
```

To learn about what addons and their options are available for a particular spell, see the [Spellbook][spellbook]

<!-- LINKS -->
[spellbook]: ./spellbook.md
