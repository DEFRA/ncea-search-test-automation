# NCEA-Geonetwork

This repository is a cloned copy of the repository https://github.com/geonetwork/core-geonetwork on the tag 4.2.8
Source: https://github.com/geonetwork/core-geonetwork/commit/e669c78201896f0b5ff3504195be7c799cff227c

This is a Java project that uses JDK 1.8, and Spring version 5.3.30
The project is built in Maven.

**How to run the project locally:**

1. To open the project for development it is best to have Intellij Community Edition:  
https://www.jetbrains.com/idea/download/download-thanks.html?platform=macM1&code=IIC  

2. Load the project by loading the pom.xml file, and select Open as project.  

3. In the terminal you will have to execute the following commands:
git submodule init   
git submodule update  
git submodule update --init  

4. Make sure the project is executing with Java 8, so File -> Project Structure -> SDK: Coretto 1.8 

5. Change the Maven configurations for install: 
In the Panel on the right side click on the big letter M,   
to open the maven configurations.  
Choose GeoNetwork opensource -> Lifecycle -> right click on "install" -> Modify run configurations ...  
In the field Run, replace the contents with this: clean install -DskipTests -DschemasCopy=true -T 2C -f pom.xml  
Apply, close. Now your modified configuration will be available to execute from:  
GeoNetwork opensource -> Run Configurations -> geonetwork[install]

  6. Change the Maven configurations for running locally:  
In the Panel on the right side click on the big letter M, to open the maven configurations.  
Choose GeoNetwork opensource -> GeoNetwork Web Module -> Plugins -> jetty -> right click on "jetty:run" -> Modify run configurations ...  
In the field Run, replace the contents with this: jetty:run -Penv-dev -f pom.xml  
Apply, close. Now your modified configuration will be available to execute from:  
GeoNetwork opensource -> GeoNetwork Web Module -> Run Configurations -> gn-web-app[jetty:run]    

7. To run the project locally you have to first build the project, with the first run configuration you created  
geonetwork[install]. After this finishes successfully, click on the second run configuration you created gn-web-app[jetty:run]

# GeoNetwork metadata plugin

TODO - Ganesh

# HELM Chart

GeoNetwork is deployed to the Azure Kubernetes service using HELM charts. <br/>
## Pre-requisites: <br/>
1. Elasticsearch should already be deployed in the AKS cluster. See repository https://github.com/DEFRA/ncea-infrastructure-core for details. GeoNetwork will need to mount the elasticsearch kubernetes secret to connect to the cluster and trust the elasticsearch generated cerficate authority. 
1. Kibana will also need to be deployed prior to GeoNetwork, the kibana host name and port is passed in from the values file. 
1. A Postgres database will need to be available that has been configured with a user with appropriate database permissions. See the NCEA wiki for further information. 

### GeoNetwork Helm Chart

The Helm chart does the following:

1. Creates a kubernetes storage class for an Azure Files Private endpoint configured Storage account that uses the RBAC model to retrieve the storage key and mount the 'metadata' volume. If the storage key is rotated it will continue to function <br/>
See 'ncea-geonetwork/charts/geonetwork/templates/geonetwork-priv-fileshare-storageclass.yaml'
   1. Create a storage account and SMB file share. For the Azure Storage account set “Allow storage account key access” and “Secure transfer required” to enabled. This is handled in the IAC.
   1. Assign the “Storage Account Contributor” RBAC role to the AKS default managed identity, to grant kubelet identity permission on the storage account hosting the file share. 
   1. Finally, ensure that the Storage class setting storeAccountKey is set to “false”. This allows the CSI driver to leverage the MI to get the storage account key. This in turn mounts the Azure File Share with the obtained key. <br/>
   See this link for more info: https://argonsys.com/microsoft-cloud/library/field-tips-for-aks-storage-provisioning/
1. Creates a kubernetes persistent volume claim using the Azure Files storage class, created in step 1.
1. Create a kubernetes persistent volume claim for the GeoNetowrk catalogue disk using the azure storage zone redundant storage class that is created by the Elasticsearch helm chart.
1. Creates an ingress rule using the NGINX class.
1. Create a kubernetes service
1. Creates the GeoNetwork deployment: 
   1. Environment variables are injected from ADO variable groups during the pipeline run.
   1. There are 3 volume mounts:
      1. GeoNetwork catalogue persistent volume.
      1. GeoNetwork metadata file share, used for harvesting transformed metadata.
      1. Elasticsearch secrets volume. This allows the import of the elasticsearch CA certificate to the java keystore and also the username and secret to use for connecting to the elasticsearch instance.

## Trusting the elasticsearch ca

1. The GeoNetwork container entrypoint script has been updated to create a new keystore and import the elasticsearch certificate authority certificate. <br/>
See '/ncea-geonetwork/core-geonetwork/docker/docker-entrypoint.sh'
1. This will create a new keystore and import the elasticsearch ca as well as the existing java keystore
1. GeoNetwork will then trust the elasticsearch instance

## Azure Pipelines 

Azure pipelines have dependencies on variable groups for each environment. See the ncea wiki for further details.

Stages are:

1. steps-build-and-test.yaml
1. steps-build-and-push-docker-images.yaml
1. steps-package-and-push-helm-charts.yaml
1. steps-deploy-helm-charts.yaml

