# Installing Phabricator on IBM Cloud

This document will describe how to install Phabricator on IBM Cloud using Kubernetes services.

### Contents

1. Introduction
2. Provision Kubernetes Cluster
3. Deploy IBM Cloud Block-Storage Plugin
4. Deploy Phabricator

### Introduction

To complete this tutorial, you should have an IBM Cloud account, if you do not have one, please [register/signup here](https://cloud.ibm.com/registration). For installing Phabricator, we have used the Kubernetes cluster, and used the IBM Cloud Block-Storage plugin for our persistent volume. Upon the completion of this tutorial, you would have the Phabricator up and running on the Kubernetes cluster.

1. Provision the Kubernetes cluster, if you have already setup one, skip to step 2.
2. Deploy the IBM Cloud Block-Storage Plugin to the created cluster, if you have already done this, skip to step 3.
3. Deploy the Phabricator.

## Step 1 - Provision Kubernetes Cluster

- Click the **Catalog** button on the top
- Select **Service** from the **Catalog**
- Search for **Kubernetes Service** and click on it


![Parse Server1](https://user-images.githubusercontent.com/5286796/106688265-c4507e80-65f3-11eb-9dbd-288903ff044a.png)

- You are now at the Kubernetes deployment page. You need to specify some information about the cluster.
- Choose either of the following plans; **standard** or **free**. The free plan only have one worker node and no subnet. To provision a standard cluster. You will   need to upgrade your account to Pay-As-You-Go
- To upgrade to a Pay-As-You-Go account, complete the following steps:
- In the console, go to Manage > Account.
- Select Account settings and click `Add credit card`.
- Enter your payment information, click Next, and submit your information
- Choose **classic** or **VPC**. Read the docs and choose the most suitable type for yourself

![Parse Server2](https://user-images.githubusercontent.com/5286796/106688262-c3b7e800-65f3-11eb-8b31-8a3bf0e7e387.png)

- Now choose your location settings,
- Choose **Geography** (continent)

![Parse Server3](https://user-images.githubusercontent.com/5286796/106688258-c1ee2480-65f3-11eb-9196-112a069239c0.png)

- Choose Single or Multizone. 
> In single zone, your data is only kept on the datacenter while on the other hand with Multizone, it is distributed to multiple zones, thus safer in an unforeseen zone failure
>
> If you wish to use Multizone, please set up your account with VRF

- If at your current location selection, there is no available Virtual LAN, a new VLAN will be created for you
- Choose a Worker node setup or use the preselected one. Set Worker node amount per zone
- Choose **Master Service Endpoint**. 

> In VRF-enabled accounts, you can choose private-only to make your master accessible on the private network or via VPN tunnel. Choose public-only to make your master publicly accessible. When you have a VRF-enabled account, your cluster is set up by default to use both private and public endpoints.
- Give desired **tags** to your cluster, click **create**
- Wait for your cluster to be provisioned
- Your cluster is ready for usage

## Step 2 - Deploy IBM Cloud Block Storage plug-in

The Block Storage plug-in is a persistent, high-performance iSCSI storage that you can add to your apps by using Kubernetes Persistent Volumes (PVs).

- Click the **Catalog** button on the top
- Select **Software** from the catalog
- Search for **IBM Cloud Block Storage plug-in** and click on it
- On the application page, click in the dot next to the cluster you wish to use
- Click on Enter or Select Namespace and choose the default Namespace or use a custom one (if you get error please wait 30 minutes for the cluster to finalize)
- Give a **name** to this workspace
- Click **install** and wait for the deployment

## Step 3 - Installing Phabricator

### Prerequisites

- Kubernetes 1.12+
- Helm 2.11+ or Helm 3.0-beta3+
- PV provisioner support in the underlying infrastructure
- ReadWriteMany volumes for deployment scaling

### Installing the Chart

To install the chart with the release name my-release:

```sh
$ helm install my-release bitnami/phabricator
```

The command deploys Phabricator on the Kubernetes cluster in the default configuration. The [Parameters](https://hub.kubeapps.com/#parameters) section lists the parameters that can be configured during installation.

The reserved IP address can be associated to the Phabricator service by specifying it as the value of the phabricatorLoadBalancerIP parameter while installing the chart.

Specify each parameter using the --set key=value[,key=value] argument to helm install. For example,

```sh
$ helm install my-release --set phabricatorUsername=admin,phabricatorPassword=password,mariadb.mariadbRootPassword=secretpassword \bitnami/phabricator
```

The above command sets the Phabricator administrator account username and password to admin and password respectively. Additionally, it sets the MariaDB root user password to secretpassword.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```sh
$ helm install my-release -f values.yaml bitnami/phabricator
```

### Persistence

The [Bitnami Phabricator](https://github.com/bitnami/bitnami-docker-phabricator) image stores the Phabricator data and configurations at the /bitnami/phabricator path of the container.

Persistent Volume Claims are used to keep the data across deployments. There is a [known issue](https://github.com/kubernetes/kubernetes/issues/39178) in Kubernetes Clusters with EBS in different availability zones. Ensure your cluster is configured properly to create Volumes in the same availability zone where the nodes are running. Kuberentes 1.12 solved this issue with the [Volume Binding Mode](https://kubernetes.io/docs/concepts/storage/storage-classes/#volume-binding-mode).

See the [Parameters](https://cloud.ibm.com/catalog/content/phabricator#parameters) section to configure the PVC or to disable persistence.

### Setting Pod's affinity

This chart allows you to set your custom affinity using the affinity paremeter. Find more infomation about Pod's affinity in the [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

As an alternative, +you can use of the preset configurations for pod affinity, pod anti-affinity, and node affinity available at the [bitnami/common](https://github.com/bitnami/charts/tree/master/bitnami/common#affinities) chart. To do so, set the podAffinityPreset, podAntiAffinityPreset, or nodeAffinityPreset parameters

The installation is done. Enjoy!
