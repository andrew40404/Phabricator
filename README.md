# Installing Parse Server on IBM Cloud

This document will describe how to install Parse Server on IBM Cloud using Kubernetes services.

**Step 1 provision Kubernetes Cluster**

- Click the **Catalog** button on the top

- Select **Service** from the **Catalog**

- Search for **Kubernetes Service** and click on it

  ![Node Exporter2](https://user-images.githubusercontent.com/5286796/106587901-2d8eae00-6570-11eb-9006-eaace4f27f60.png)

- You are now at the Kubernetes deployment page. You need to specify some details about the cluster

- Choose a plan **standard** or **free** , the free plan only has one worker node and no subnet, to provision a standard cluster, you will need to upgrade your account to Pay-As-You-Go

- To upgrade to a Pay-As-You-Go account, complete the following steps:

- In the console, go to Manage > Account.

- Select Account settings; and click Add credit card.

- Enter your payment information, click Next, and submit your information

- Choose **classic** or **VPC** , read the docs and choose the most suitable type for yourself

  ![Node Exporter1](https://user-images.githubusercontent.com/5286796/106587895-2cf61780-6570-11eb-88a3-733e5c110279.png)

- Now choose your location settings,

- Choose **Geography** (continent)

![Node Exporter3](https://user-images.githubusercontent.com/5286796/106587887-2b2c5400-6570-11eb-9006-0df5feae0762.png)

-   Choose 	Single or Multizone, in single zone your data is only kept in on 	datacenter, on the

​      other hand with Multizone it is distributed to multiple zones, thus safer in an unforeseen

​      zone failure

- If you wish to use Multizone please set up your account with[VRF

- If at your current location selection, there is no available Virtual LAN, a new VLAN will be created for you
- Choose a Worker node setup or use the preselected one, set Worker node amount per zone
- Choose **Master Service Endpoint**. In VRF-enabled accounts, you can choose private-only to make your master accessible on the private network or via VPN tunnel. Choose public-only to make your master publicly accessible. When you have a VRF-enabled account, your cluster is set up by default to use both private and public endpoints.
   Give desired **tags** to your cluster, for more information visit tags
- Click **create**
   • Wait for your cluster to be provisioned
   • Your cluster is ready for usage

**Step 2 Deploy IBM Cloud Block Storage plug-in**

The Block Storage plug-in is a persistent, high-performance iSCSI storage that you can add to your apps by using Kubernetes Persistent Volumes (PVs).

- Click the **Catalog** button on the top

- Select **Software** from the catalog

- Search for **IBM Cloud Block Storage plug-in** and click on it
  
   • On the application page Click in the dot next to the cluster, you wish to use
   • Click on Enter or Select Namespace and choose the default Namespace or use a custom one (if you get error please wait 30 minutes for the cluster to finalize)
   
   
   
- Give a **name** to this workspace

- Click **install** and wait for the deployment



# **Step 3 **Installing Parse Server

**Helm Charts to deploy Parse Server in Kubernetes**

```sh
$ helm repo add bitnami-ibm https://charts.bitnami.com/ibm
```

```sh
$ helm install my-release bitnami-ibm/parse
```

**To reserve a public IP address on GKE:**

```sh
$ gcloud compute addresses create parse-public-ip
```

**Parameters**

The following table lists the configurable parameters of the Parse chart and their default values.

Global Parameters



| **Parameter**           | **Description**                                 | **Default**                                           |
| ----------------------- | ----------------------------------------------- | ----------------------------------------------------- |
| global.imageRegistry    | Global Docker image registry                    | nil                                                   |
| global.imagePullSecrets | Global Docker registry secret names as an array | [] (does not add image pull secrets to deployed pods) |
| global.storageClass     | Global storage class for dynamic provisioning   | nil                                                   |

**Deploy your Cloud functions with Parse Cloud Code**

The [Bitnami Parse](https://github.com/bitnami/bitnami-docker-parse) image allows you to deploy your Cloud functions with Parse Cloud Code (a feature which allows running a piece of code in your Parse Server instead of the user's mobile devices). 

To add your custom scripts, they must be located inside the chart folder files/cloud so they can be consumed as a ConfigMap.

Alternatively, you can specify custom scripts using the cloudCodeScripts parameter as dict.

In addition to these options, you can also set an external ConfigMap with all the Cloud Code scripts. This is done by setting the existingCloudCodeScriptsCM parameter.

Note that this will override the two previous options.

Adding extra environment variables

In case you want to add extra environment variables (useful for advanced operations like custom init scripts), you can use the extraEnvVars (available in the server and dashboard sections) property.

extraEnvVars:

 \- name: PARSE_SERVER_ALLOW_CLIENT_CLASS_CREATION

   value: true

Alternatively, you can use a ConfigMap or a Secret with the environment variables. To do so, use the extraEnvVarsCM or the extraEnvVarsSecret values.

**Deploying Extra Resources**

There are cases where you may want to deploy extra objects, such as KongPlugins, KongConsumers, amongst others. For this case, the chart allows adding the full specification of other objects using the extraDeploy parameter.

The following example would activate a plugin at deployment time.

Extra objects to deploy (value evaluated as a template)

```sh
extraDeploy: |-
```

 ```sh
\- apiVersion: rbac.authorization.k8s.io/v1
 ```

   ```sh
kind: RoleBinding
   ```

  ```sh
metadata:
name: {{ include "common.names.fullname" . }}-privileged

namespace: {{ .Release.Namespace }}

labels: {{- include "common.labels.standard" . | nindent 6 }}

roleRef:

apiGroup: rbac.authorization.k8s.io

kind: ClusterRole

name: cluster-admin

subjects:

\- kind: ServiceAccount

   name: default

   namespace: {{ .Release.Namespace }}
  ```

​     

Both container images and chart can be upgraded by running the command below:

```sh
$ helm upgrade my-release bitnami-ibm/parse
```

If you use a previous container image (previous to **3.1.2-r14** for Parse or **1.2.0-r69** for Parse Dashboard), disable the securityContext by running the command below:

```sh
$ helm upgrade my-release bitnami-ibm/parse --set server.securityContext.enabled=false,das
```


 