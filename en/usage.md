Title: conjure-up | Advanced Spell Summoning
table_of_contents: True

# Advanced Spell Summoning

**conjure-up** supports summoning spells that are in various stages of
development by the use of channels, or from remote VCS repositories, and from a local
directory on your filesystem.

## Channels

Spells can be in various stages of development so channels allow users to decide
if they want the latest features, or be part of a beta test; others may want to
only install the most reliable software.

For example, to deploy a spell from the **beta** channel run:

```bash
conjure-up canonical-kubernetes --channel beta
```

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

## Automating conjure-up

**conjure-up** is meant to be a teaching tool in addition to a full blown
application deployment tool. By Default **conjure-up** will walk you through
the entire deployment process and help you understand what it is you are
deploying.

Where this doesn’t make sense is if you are wanting to deploy your application
in an automated fashion. For example, integrating the deployment with a Jenkins
CI server.

For these cases **conjure-up** provides the ability to customise the entire
spell through a [Conjurefile][conjurefile].

!!! Note:
    Keep in mind you’ll need to have credentials defined, see
    [Juju credentials][jujucredentials] for more details.

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
[charmstore]: https://jujucharms.com/store
[conjurefile]: ./conjurefile.md
