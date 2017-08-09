Title: Spell walkthrough
table_of_contents: True

# Spell Walkthrough

Follow through our screenshot walkthrough of deploying **Canonical
Distribution of Kubernetes**, Enterprise Kubernetes, anywhere.

## Spell selection

Initially, you will be provided with a list of available spells that can be
deployed. For this walkthrough we will select **Canonical Kubernetes**.

!!! Note:
    Once a Spell is selected you can view its **README** at any time by
    pressing `R`

***Figure: Spell selection***

![Spell selection][spellselection]

## Cloud Selection

Next, a list of supported clouds will be presented. You can choose from a public
cloud, or you can create or connect to a private cloud.

***Figure: Cloud selection***

![Cloud selection][cloudselection]

## Credentials

**conjure-up** will load any existing credentials for the selected cloud or give
the ability to enter new ones

***Figure: Credentials Selection***

![Credentials Selection][credentialsselection]

If no credentials are found, selecting **Add a New credential** will prompt you for those.

!!! Note: If you are using a private cloud, you may also need to provide the
endpoint or connection information here.

***Figure: Add new credential***

![Add New Credential][newcredential]

## Region Selection

Most clouds provide you with a list of regions where you can deploy your spell.

***Figure: Region selection***

![Region Selection][regionselection]

!!! Note:
    If **vsphere** is selected, then those regions are generated from vSphere's
    known datacenters. For more information on using **conjure-up** with VMware vSphere, [click here](providers/vsphere.html).

## Configure Spell

These additional steps encapsulate the operational tasks to perform to your
deployment in order to start using your big software. In the below figure you’ll
notice that this walks you through downloading the required **kubectl** tool to
work with your new cluster. Additionally, it’ll contact your cluster and grab
the necessary information to display for you on the following summary screen.

***Figure: Steps Configuration***

![Steps configuration][stepsconfig]


## Application List

Once a cloud is selected you will be presented with a list of applications that
make up the **Canonical Kubernetes** deployment. This screen allows you to
deploy each application individually or make additional configuration changes
to the selected application (covered in the next step).

***Figure: Application list***

![Application list][applicationlist]

## Application configuration

In the configuration screen for the application, you have the ability to
configure certain aspects prior to deployment. For example, in **Figure 4** you
can increase the amount of units to deploy of Elasticsearch.

***Figure: Application Config***

![Application configuration][appconfig]

## Bootstrap

Once the applications are deployed, and if no previously bootstrapped cloud
exist, you will be presented with a wait screen that gives you the status of
the current bootstrap.

***Figure:  Bootstrap Wait Screen***

![Bootstrap wait][bootstrapwait]

## Deployment status

After the bootstrap process is complete the applications will begin their
deployment tasks. This includes installing the necessary bits onto the
allocated machines, setting their relations between the applications, and
verifying that each application starts successfully. You’ll notice that once
the applications are ready they will have a green checkmark beside them.

***Figure: Deployment Status Screen***

![Deploy status][deploystatus]

## Summary

Finally, the summary screen presents you with all the information necessary to
access and start using your big software.

***Figure: Summary***
<< update screenshot >>

!!! Note:
    In the above figure you can see that Cloud Native Integration has been
    enabled and is now available to your Kubernetes cluster. Please see [k8s and
    aws cloud native integration](cni/k8s-and-aws.html) for more information.

Pressing `Q` will return you back to the shell with your deployment left
intact.

!!! Note:
    Once you've exited **conjure-up** you can still manage your
    deployments via `juju` command. Please have a look at [the Juju documentation](https://jujucharms.com/docs/) for more information.

<!-- LINKS -->

<!-- IMAGES -->
[spellselection]: ../media/spell-selection.png
[cloudselection]: ../media/cloud-selection.png
[credentialsselection]: ../media/choose-credential.png
[newcredential]: ../media/add-credential.png
[regionselection]: ../media/choose-region.png
[applicationlist]: ../media/application-list.png
[appconfig]: ../media/application-config.png
[bootstrapwait]: ../media/bootstrap-wait.png
[deploystatus]: ../media/deploy-status.png
[stepsconfig]: ../media/steps-config.png
[summary]: ../media/summary.png

