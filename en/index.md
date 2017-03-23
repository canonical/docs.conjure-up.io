Title: conjure-up | User manual
TODO: Needs a considerable overhaul
      Document should be renamed
      
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

### Hardware requirements

For **Public Cloud** deployments hardware requirements (*constraints*) are
handled by the Spell authors and will automatically be allocated during deploy.

For **localhost** deployments the following setup is recommended:

- 2 cores
- 16G RAM
- 32G Swap
- 250G SSD with a separate block device for ZFS. Our recommendation for that
  device is 100G.

### Installing conjure-up

`conjure-up` is available on both Ubuntu Trusty 14.04 LTS and Ubuntu Xenial
16.04 LTS

```bash
sudo snap install conjure-up --classic
```

!!! Note: 
    If above command fails you’ll want to make sure **snapd** is installed with
    `sudo apt install snapd`

Trusty users will need to perform some additional steps prior to getting the
snappy version installed.

To get a working **conjure-up** install on Trusty, the following needs to be
run:

```bash
sudo apt-get install snapd
sudo groupadd lxd && sudo usermod -a -G lxd $USER
sudo reboot
sudo snap install conjure-up --classic
```

#### Beta and development versions 

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

#### Users of LXD 

**conjure-up** currently only supports running a single installation of LXD.
This means systems with LXD pre-installed, like Ubuntu Xenial Server, should not
have the snapped version of LXD installed.

!!! Note: 
    If you’ve never done anything with [Snaps][snappy] before feel free to skip this section.

You can opt to use either or as **conjure-up** will support LXD versions 2.0.8
and above. To verify that you do only have a single installation of LXD, check
that the output from the `snap list` command does not have `lxd` listed:

```
Name  Version  Rev  Developer  Notes
core  16.04.1  888  canonical  -
```

If `lxd` is listed in the above output, make sure you do not have the Debian
package installed by running:

```bash
dpkg -l lxd
```

In order to fix this problem you’ll want to remove one of those:

For the snap version:

```bash
sudo snap remove lxd
```
For the Debian package:

```bash
sudo apt-get remove lxd lxd-client
sudo apt-get purge lxd
```

### Summon a Spell

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

#### Uninstalling

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
