Title: conjure-up | Troubleshooting

# Troubleshooting

## Logging

Logs are written to `journald` by default. Logs may be viewed with the command:

```bash
journalctl |grep conjure-up
```

!!! Note:
    There is also a log file that is written to
    `\$HOME/.cache/conjure-up/conjure-up.log`

## Unicode

If the system running `conjure-up` does not have its locale defined to
`UTF-8` a failure will occur similar to:

```
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe2 in position 2201:
ordinal not in range(128)```

To fix you will need to set your locale accordingly. Run the following command
to view current settings:

```bash
locale
```

The output from the above command will appear similar to the following:

```no-highlight
LANG=en_US.UTF-8
LANGUAGE=en_US
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

## Common Spell Problems

### Applications in Error state

One of the biggest reasons why applications will error out during deployment is
due to not having enough disk space available. Please make sure you meet the
[Hardware Requirements][hardware] prior to deploying spells.

## LXD

### Failed deployments if IPv6 is enabled

Currently **conjure-up** does not support the use of IPv6. If you are running
LXD and have a bridge that has IPv6 enabled **conjure-up** will fail. The
output will include an explanation why and information for disabling IPv6.

### Disable IPv6 on LXD versions 2.2 and below

LXD version 2.0 is the default version on Ubuntu Xenial 16.04. To disable IPv6
you’ll need to edit `/etc/default/lxd-bridge` and make sure the following
values are reflected below:

```no-highlight
# IPv6
## IPv6 address (e.g. 2001:470:b368:4242::1)
LXD_IPV6_ADDR=""

### IPv6 CIDR mask (e.g. 64)LXD_IPV6_MASK=""

### IPv6 network (e.g. 2001:470:b368:4242::/64)LXD_IPV6_NETWORK=""

### NAT IPv6 trafficLXD_IPV6_NAT="false"
```

### Disable IPv6 on LXD versions 2.4 and above

To disable IPv6 on your LXD bridge (`lxdbr0` by default) run the following:

```bash
lxc network set lxdbr0 ipv6.nat false
lxc network set lxdbr0 ipv6.address none
```

## OpenStack


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
