# Spell Walkthrough {#walkthrough}

Follow through our screenshot walkthrough of deploying **The Canonical
Distribution of Kubernetes**, Enterprise Kubernetes, anywhere.

### Spell Selection 
Initially, you will be provided with a list of available spells that can be
deployed. For this walkthrough we will select **Canonical Kubernetes**.

!!! Note: Once a Spell is selected you can view its **README** at any time by pressing
kbd:\[R\]

![Spell selection]

### Cloud Selection 
Next, a list of publicly supported clouds will be presented.

![Cloud selection]

### Application List
Once a cloud is selected you will be presented with a list of applications
that make up the **Canonical Kubernetes** deployment. This screen allows you
to deploy each application individually or make additional configuration
changes to the selected application (covered in [section\_title]).

![Application list]

### Application Configuration 
In the configuration screen for the application you have the ability to
configure certain aspects prior to deployment. For example, in **Figure 4**
you can increase the amount of units to deploy of Elasticsearch.

![Application Config]

### Bootstrap 
Once the applications are deployed and if no previously bootstrapped cloud
exist you will be presented with a wait screen that gives you the status of
the current bootstrap.

!!! Note: If an existing cloud is already bootstrapped you will not see this view.

![Bootstrap Wait Screen]

### Deployment Status 
After the bootstrap process is complete the applications will begin their
deployment tasks. This includes installing the necessary bits onto the
allocated machines, setting their relations between the applications, and
verifying that each application starts successfully. You’ll notice that once
the applications are ready they will have a green checkmark beside them.

![Deployment Status Screen]

### Additional Application Tasks 
This is the real benefit of **conjure-up**. These additional steps encapsulate
the operational tasks to perform to your deployment in order to start using
your big software. In **Figure 7** you’ll notice that this walks you through
downloading the required **kubectl** tool to work with your new cluster.
Additionally, it’ll contact your cluster and grab the necessary information to
display for you on the [section\_title][1].

![Steps Configuration]

### Summary 
Finally, the summary screen presents you with all the information necessary to
access and start using your big software. In **Figure 8** we provide you with
how to access and use your **kubectl** binary along with the Kubernetes
**cluster-info** and how to access the **Kibana** dashboard for viewing things
such as Filebeat and Topbeat.

![Summary]

Pressing kbd:\[Q\] will return you back to the shell with your deployment left
intact.

  [Spell selection]: canonical-kubernetes/spell-selection.png
  [Cloud selection]: canonical-kubernetes/cloud-selection.png
  [section\_title]: #application-config
  [Application list]: canonical-kubernetes/application-list.png
  [Application Config]: canonical-kubernetes/application-config.png
  [Bootstrap Wait Screen]: canonical-kubernetes/bootstrap-wait.png
  [Deployment Status Screen]: canonical-kubernetes/deploy-status.png
  [1]: #summary-screen
  [Steps Configuration]: canonical-kubernetes/steps-config.png
  [Summary]: canonical-kubernetes/summary.png
