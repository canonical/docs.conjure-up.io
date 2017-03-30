Title: conjure-up | Developer Manual
table_of_contents: True

# Developer Manual

At the heart of **conjure-up** are spells. They consist of all the information
required to deploy your applications and walk the user through getting started
as quickly as possible.

There are 2 main parts that make up a spell. The first being a **Juju bundle**,
these are your blueprints for defining the amount of units for each application
along with any default configuration options you wish to expose to the user for
modification.

The second part is the additional data added to the spell to be **conjure-up**
enabled. This contains your metadata which describes the spell and a set of
steps that you define for a user to walkthrough. This allows you as a spell
author to ensure that a user can learn about your application and at the end
know exactly what to do next in order to make the most out of your application.

It is recommended to learn the following [Anatomy of a conjure-up
spell][anatomy] section in order to get started building your spell!

## Anatomy of a conjure-up spell

We will start with the directory layout of a typical spell. Some of this will
be familiar as we build on the existing Juju bundle specification.

```no-highlight
my-super-application>
   - bundle.yaml
   - README.md
   - metadata.yaml
   - steps/
     - 00_deploy-done
     - 00_pre-deploy
     - step-01_my_first_step
     - step-01_my_first_step.yaml
```

- If `bundle.yaml` exists in the spell directory it will be used, otherwise a
  `bundle-name` is required. See Juju's
  [Using and Creating Bundles][bundles] documentation for more information.

- `metadata.yaml` contains metadata for the spell. See the following section on
  [Spell metadata][metadata] for details.

- The `steps` directory stores executable scripts to be run prior to
  deployment, to check when a deployment is finished, and steps to be processed
  for configuring deployment. See [Steps][steps] for more information.

## Spell metadata

The spell metadata contains properties that are useful for conjure-up to
process certain aspects of a spell. Available properties are as follows:

**friendly-name**:  This is used during the spell selection within conjure-up.

**cloud-whitelist**: A list of Public clouds available for a spell.

**cloud-blacklist**: A list of Public clouds that are known to not work with a spell.

**options-whitelist**: Additional charm options to be exposed for editing within conjure-up.

**bundle-name**: A name of a bundle associated with this spell, for example, **canonical-kubernetes**

*metadata.yaml example*:

```yaml
cloud-whitelist:
- maas
friendly-name: OpenStack with NovaKVM
options-whitelist:
  keystone:
  - admin-password
  lxd:
  - block-devices
  mysql:
  - max-connections
  neutron-gateway:
  - ext-port
  nova-compute:
  - virt-type
bundle-name: openstack-base
```

## Steps

During a deployment, **conjure-up** will always check for and run a predefined
set of scripts:

- [`00_pre-deploy`][predeploy]: This script contains necessary instructions for
  configuring a system prior to a deployment.

- [`00_deploy-done`][deploydone]: This script will contain instructions for verifying that
  deployment has finished and is ready for its next steps.

- `step-##_a_step`: This is an alpha numerical script that will be executed
  in order to perform any actions necessary to provide a turn key and usable
  deployment.

### 00_pre-deploy

In some cases, like OpenStack with NovaLXD modifications need to be made to the
LXD container profile to allow for certain kernel modules and networking to be
enabled. This needs to happen prior to any applications being deployed in the
container.

An example of `00_pre-deploy` for OpenStack with NovaLXD:

```bash
#!/bin/bash

set -eu

. $CONJURE_UP_SPELLSDIR/sdk/common.sh

if [[ "$JUJU_PROVIDERTYPE" == "lxd" ]]; then
    debug "Running pre-deploy for $CONJURE_UP_SPELL"
    sed "s/##MODEL##/$JUJU_MODEL/" $(scriptPath)/lxd-profile.yaml | lxc profile edit "juju-$JUJU_MODEL" || exposeResult "Failed to set profile" $? "false"
fi

exposeResult "Successful pre-deploy." 0 "true"
```

Notes on the above script:

- The `CONJURE_UP_SPELLSDIR` environment variable is already exposed and is
  useful if wanting to use the provided spell SDK.

- The `$JUJU_PROVIDERTYPE` is exposed as an environment variable to check the
  type of public cloud this script is running in.

- Since we are doing this on a container it makes sense to only work with the
  `LXD` type.

- The `sed` command performs an in-place update of the LXD profile. Due to the
  nature of LXD this profile will be available immediately even on containers
  that have already started.

- The final line is a helper function that's part of the builtin hooklib for
  writing steps. This lets conjure-up know that this pre-deploy task has
  completed without error.

### 00_deploy-done

Before we can process any additional steps we need to wait for all the
deployed applications to become in a `ready` state. Below demonstrates a
couple of ways to check for an error of the unit or machine:

An example of `00_deploy-done` for OpenStack with NovaLXD:

```bash
#!/bin/bash
set -eu

. $CONJURE_UP_SPELLSDIR/sdk/common.sh

juju wait -m $JUJU_CONTROLLER:$JUJU_MODEL

exposeResult "Applications Ready" 0 "true"
```

- `juju wait` is included with conjure-up and allows us to easily determine
   when all applications are in a ready state.

- All scripts must end with JSON output describing the result, in this case
  the Applications are in a ready state.

## Post processing

There are 2 sections to post processing. The first section is the `step
metadata`, this metadata provides conjure-up some context about what to display
to the user for configuration and how to pass that information to the
processing script.

Steps are created alpha numerically and have 2 files associated. The first file
being the step script named `step-01_keypair`. The second file is the metadata
for that step named `step-01_keypair.yaml`.

The metadata for a step consists of:

- `title`: A short title of the step.
- `description`: A summary of what this steps does.
- `viewable`: Boolean to indicate if this steps summary and actions are seen
  within conjure-up.
- `required`: Boolean to indicate that this step is a requirement and has to be
  run.
- `sudo`: Boolean to indicate if this step requires elevated priviledges.
- `additional-input`: Additional configuration variables that can be changed by
  the user within conjure-up.

## Additional input

This section of the step describes the configuration object and how it is to be
displayed to the user within conjure-up and how a step would utilize the result
from the user input.

The additional input has the following properties:

- `label`: Rendered label describing the input.
- `key`: The result of input is stored in this key which is exposed via
  environment variables.
- `type`: Type of input.
- `default`: Default value for input.

A full example of `step-01_keypair.yaml`:

```yaml
title: SSH
description: |
  Import SSH keypairs into OpenStack. This allows you to access the newly deployed instances via SSH with your current user. If you are not sure about the location of a ssh key leave it as is and we will create one automatically.
viewable: True
required: True
additional-input:
  - label: SSH public key path
    key: SSHPUBLICKEY
    type: text
    default: ~/.ssh/id_rsa.pub
```

A full example of `step-01_keypair` script:

```bash
. $CONJURE_UP_SPELLSDIR/sdk/common.sh

_ssh_public_key=$(expandPath $SSHPUBLICKEY)
debug "Environment Variables: $_ssh_public_key"

tmpfile=$(mktemp)
debug "Created tmpfile: $tmpfile"

cat <<EOF> $tmpfile
sudo apt update > /dev/null 2>&1
sudo apt -qyf install python3-openstackclient > /dev/null 2>&1
EOF

# write credentials
$(scriptPath)/share/novarc >> $tmpfile

# include lib
cat $(scriptPath)/share/common.sh >> $tmpfile

if [ ! -f $_ssh_public_key ]; then
    debug "Couldnt find $_ssh_public_key, attempting to create one: " ${_ssh_public_key%.*}
    ssh-keygen -N '' -f ${_ssh_public_key%.*} > /dev/null 2>&1
    debug "ssh-keygen result: $?"
fi

# Set ssh public key on controller
echo "export SSHPUBLICKEY=$HOME/.ssh/$(basename $_ssh_public_key)" >> $tmpfile

# write final script
cat $(scriptPath)/share/keypair.sh >> $tmpfile

debug "Creating .ssh directory on controller node"
juju ssh -m $JUJU_CONTROLLER:$JUJU_MODEL nova-cloud-controller/0 "mkdir -p ~/.ssh && chmod 700 ~/.ssh"

debug "SCPing over ${_ssh_public_key%.*} to controller node"
juju scp -m $JUJU_CONTROLLER:$JUJU_MODEL ${_ssh_public_key%.*}* nova-cloud-controller/0:.ssh/.
debug "scp result: $?"

debug "SCPing over $tmpfile controller node"
juju scp -m $JUJU_CONTROLLER:$JUJU_MODEL $tmpfile nova-cloud-controller/0:keypair.sh
debug "scp result: $?"

juju ssh -m $JUJU_CONTROLLER:$JUJU_MODEL nova-cloud-controller/0 "bash keypair.sh"
```

- `$SSHPUBLICKEY` is the environment variable that was defined in the `key` section
    of `step-01_keypair.yaml`.
- `JUJU_CONTROLLER` and `JUJU_MODEL` are exposed through environments variables
  for all steps. These can be relied on to make sure you are always operating
  in the most current juju environment.

The full source for this spell can be found at our [GitHub spells
registry][spellsrepo].

<!-- LINKS -->

[anatomy]: ./dev-manual.md#anatomy-of-a-conjure-up-spell
[bundles]: https://jujucharms.com/docs/stable/charms-bundles
[metadata]: ./dev-manual.md#spell-metadata
[steps]: ./dev-manual.md#steps
[predeploy]: ./dev-manual.md#00_pre-deploy
[deploydone]: ./dev-manual.md#00_deploy-done
[spellsrepo]: https://github.com/conjure-up/spells/tree/master/openstack-novalxd
