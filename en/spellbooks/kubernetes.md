Title: conjure-up | Spellbooks | Kubernetes
table_of_contents: True

# Kubernetes Spell

## Steps

### 00_process-providertype

Provider specific alterations

#### Phases

**before-deploy** - If Kubernetes is being deployed on a Localhost (LXD)
provider, some modifications to LXD profiles are required, this phase handles
those modifications.

**after-deploy** - If Localhost (LXD) provider set **proxy-mode=userspace** in
the `kubernetes-worker`.

#### Options

No configuration options for this step.

### 01_select-network

Choose a network stack

#### Phases

**after-input** - Allows selecting which network stack to choose from.

#### Options

**networkplugin** - Choice of **Flannel** or **Calico** as a network plugin.

#### Example Conjurefile

```yaml
spell: canonical-kubernetes
steps:
  01_select-network:
    networkplugin: calico
```

### 02_get-kubectl

Downloads **kubectl**

#### Phases

**after-deploy** - Performs the download of kubectl and sets up the proper **kube configs**.

#### Options

No configuration options for this step.

### 03_cluster-info

Display the current Kubernetes cluster information

#### Phases

**after-deploy** - Queries the Kubernetes cluster information and displays it to the user.

#### Options

No configuration options for this step.

### 04_enable-cni

Enables Cloud Native Integration based on the Public Cloud selected

#### Phases

**before-config** - Applies the proper cloud overlay for deploying the correct
cloud charm for future interactions with the public cloud of choice.

**before-wait** - Deploys the cloud charm and applies the correct cloud
credentials for future interactions with the public cloud of choice.

**after-deploy** - Verifies cloud native integration is enabled and displayed to
the user.

#### Options

No configuration options for this step.

## Addons

### Graylog

#### 01_install-graylog

Installs Graylog the enterprise log management software.

##### Phases

**after-deploy** - Configures the Graylog UI and displays it to the user.

#### Options

No configuration options for this addon.

### Helm

#### 01_install-helm

Installs Helm, the package manager for Kubernetes

##### Phases

**after-deploy** - Performs the helm installation

##### Options

**helm_version** - The version of Helm to be installed

##### Example Conjurefile

```yaml
spell: canonical-kubernetes
addons:
  helm:
    01_install-helm:
      helm_version: v2.8.1
```

## Limitations

### Deploying Kubernetes on Localhost (LXD) provider.

There are some unknown issues when attempting to deploy Kubernetes on LXD with a
storage of **ZFS** or **btrfs**. The most common error is "Waiting for
kube-system pods to start".

Until a solution is found, please only use the **dir** storage backend when
configuring LXD.

