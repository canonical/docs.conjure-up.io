Title: conjure-up | User manual
TODO: Needs a considerable overhaul
      Document should be renamed
table_of_contents: True

# User Manual

**conjure-up** is a thin layer spanning a few different underlying technologies
- [Juju][juju], [MAAS][maas] and [LXD][lxd].

**conjure-up** provides you with a streamlined, turnkey solution. In order to
provide that streamlined approach, **conjure-up** makes use of processing
scripts. These scripts are executed at 3 different times in the deployment:
just after a Juju bootstrap, just before a Juju deploy, and right after a Juju
deploy.

Processing scripts give you the flexibility to alter LXD profiles in order to
expose additional network interfaces to Neutron services, import images into
Glance once the service is available, or notifying the Deployment status screen
that your solution is ready and can be viewed at a specific URL.

With these powerful concepts you can package up the solution that can then be
provided to coworkers who can easily deploy your solutions in any Public
Cloud, MAAS, or LXD.

## Getting started

## Hardware requirements

For **Public Cloud** deployments hardware requirements (*constraints*) are
handled by the Spell authors and will automatically be allocated during deploy.

For **localhost** deployments the following setup is recommended:

- 2 cores
- 16G RAM
- 32G Swap
- 250G SSD with a separate block device for ZFS. Our recommendation for that
  device is 100G.

## Installing conjure-up

`conjure-up` is available on Ubuntu Xenial 16.04 LTS and macOS

### Ubuntu
```bash
sudo snap install conjure-up --classic
```
!!! Note:
    If above command fails you’ll want to make sure **snapd** is installed with
    `sudo apt install snapd`

### macOS
```
brew install conjure-up
```

## Beta and development versions

If you want to preview of the next release, the latest beta version can be
installed with the following command:

```bash
sudo snap install conjure-up --classic --beta
```

For the most recent changes, install the `edge` release:

```bash
sudo snap install conjure-up --classic --edge
```

If you have **conjure-up** already installed, you can update to a different
snap channel with:

```bash
sudo snap refresh conjure-up --classic --edge
```
or

```
sudo snap refresh conjure-up --classic --beta
```

## Summon a Spell

To deploy solutions such as OpenStack you will summon a spell:

```bash
conjure-up openstack
```

To see a list of all available spells run:

```bash
conjure-up
```

!!! Note:
    Several remote locations are supported - please see [Advanced Spell
    Summoning][advancedspells] for further details

## Uninstalling

To remove deployments:

```bash
conjure-down
```

To uninstall **conjure-up** itself:

```bash
sudo snap remove conjure-up
```

<!-- LINKS -->
[juju]: https://jujucharms.com
[maas]: https://maas.io/
[lxd]: https://linuxcontainers.org/lxd/
[trusty]: http://releases.ubuntu.com/14.04/
[xenial]: http://releases.ubuntu.com/16.04/
[snappy]: https://snapcraft.io/
[applist]: ./index.md#application-list
[advancedspells]: ./usage.md

<!-- IMAGES -->
