Title: conjure-up | Advanced Spell Summoning

# Advanced Spell Summoning

**conjure-up** includes several spells in addition to supporting summoning
spells from several remote repositories and from a local directory on your
filesystem.

## GitHub and BitBucket

Not quite ready to push your spell to the charm registry? That’s ok, simply
push your spell to GitHub and **conjure-up** can deploy from there:

```bash
conjure-up battlemidget/ghost
```

This would pull from GitHub repo <https://github.com/battlemidget/ghost>.

## Remote web server

**conjure-up** will also support downloading directly from a webserver. For
example, if you have your spell zipped up and stored at
`http://example.com/my-conjure-spell.zip` you could install it like so:


```bash
conjure-up http://example.com/my-conjure-spell.zip
```

## Local Filesystem

Passing in either the directory path of the spell or if the current working
directory is a spell:

```bash
conjure-up ~/spells/openstack/openstack-novalxd
```

Or from **cwd**

```bash
~/spells/openstack/openstack-novalxd> conjure-up .
```

## Running in headless mode

**conjure-up** is meant to be a teaching tool in addition to a full blown
application deployment tool. By Default **conjure-up** will walk you through
the entire deployment process and help you understand what it is you are
deploying.

Where this doesn’t make sense is if you are wanting to deploy your application
in an automated fashion. For example, integrating the deployment with a Jenkins
CI server.

For these cases **conjure-up** provides a headless mode.

To deploy in a headless mode you’ll need to have credentials defined if
deploying to a Public Cloud or make sure LXD is configured if deploying to
Localhost.

To deploy OpenStack to a Localhost provider:

```bash
conjure-up openstack-novalxd localhost
```

If we want to deploy to a cloud like AWS:

```bash
conjure-up canonical-kubernetes aws
```
!!! Note:
    Keep in mind you’ll need to have credentials defined, see
    [Juju credentials][jujucredentials] for more details.

## Customising deployment phases

Currently, if no controller or model is defined during a headless install they
will be auto-generated based on the spell and cloud chosen.

However, those controllers and models can be defined to better reflect your
deployment preferences. For example, you have a controller sitting in a data
centre named **dc1** and it houses 3 deployments named **test-deploy,
stage-deploy, and production-deploy**.

To accomplish a naming strategy to reflect this design you can pass in a third
and fourth argument to **conjure-up** to define those phases:

```bash
conjure-up canonical-kubernetes aws dc1 test-deploy
conjure-up canonical-kubernetes aws dc1 stage-deploy
conjure-up canonical-kubernetes aws dc1 production-deploy
```

## Customising headless mode

Post deployment actions are exposed to the environment via environment
variables. Some actions may require you to input data depending on what is
required. An example would be input the path of your public ssh key so that
your OpenStack deployment can make those available to the compute nodes.

To see what environment variables you can set prior to running a headless mode
install, run the following:

```bash
conjure-up --show-env openstack-novalxd localhost
```

You should see output similar to:

```no-highlight
Available environment variables:

+--------------+-------------------+---------------------------------------------------------+
| ENV          | DEFAULT           |
|
+--------------+-------------------+---------------------------------------------------------+
| SSHPUBLICKEY | ~/.ssh/id_rsa.pub | Import SSH keypairs into OpenStack. This allows you to  |
|              |                   | access the newly deployed instances via SSH with your   |
|              |                   | current user. If you are not sure about the location of |
|              |                   | a ssh key leave it as is and we will create one         |
|              |                   | automatically.
|
+--------------+-------------------+---------------------------------------------------------+

See http://conjure-up.io/docs/en/users/#running-in-headless-mode for more
information on using these variables to further customise your deployment.
```

In order to change it from it’s default of **\~/.ssh/id\_rsa.pub** you would
simply do:

```bash
SSHPUBLICKEY=/home/bob/my-ssh-key.pub conjure-up openstack-novalxd localhost
```

## Running conjure-up remotely

If you ssh into a separate machine to run **conjure-up** and you deploy spells
such as openstack-novalxd to localhost and you want to be able to access things
like the `openstack dashboard`.

The simplest way to accomplish this is on your local machine run the
following:


```bash
sudo apt install sshuttle
sshuttle -r <remote-host-ip> <subnet-of-localhost-deployment>
```

The subnet of localhost is going to be what **conjure-up** configured the
network bridge for. By default this is set to **10.0.8.1/24**. Lets also say
the remote host’s ip is **172.16.0.5** so you’ll want to do the following

```bash
sshuttle -r 172.16.0.5 10.0.8.1/24

[local sudo] Password:
client: Connected.
```

It will then ask you to enter your sudo password and once complete will let you
know that the sshuttle is connected. From there you can open your web browser
and access the horizon dashboard as if it was running on your local machine.

Assuming the openstack-dashboard was deployed to a machine whose ip is
10.0.8.15, you could run:

```bash
xdg-open https://10.0.8.15/horizon
```

<!-- LINKS -->
[jujucredentials]: https://jujucharms.com/docs/stable/credentials
