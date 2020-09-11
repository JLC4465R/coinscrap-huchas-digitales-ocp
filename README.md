# coinscrap-huchas-digitales-ocp

##

## Architecture Diagram

##

![Screenshot](README.png)

##

# OCP Installation

# 1. PRE Tasks

### Create the Project/Namespace

##

```
oc new-project coinscrap-huchas-digitales
```

##

##### Enter in the new OCP created Project/Namespace
```
oc project coinscrap-huchas-digitales
```

##

### Set Permissions for use root POD's in the Namespace/Project for the 'registry-ui' and 'registry-ui-adm' Apps, and allow these POD's to use CORS from Angular UI for communicates with 'registry' App, who allows enable the API Delete for Images and grant permissions for use all features of 'registry' POD
```
oc adm policy add-scc-to-user anyuid -z default 
```

##

### Create one Secret for allow that Templates and Builds use the Github Repository Credentials for clone the Apps when developers make changes and updates

##

##### Go to the OCP Console and select the Project 'coinscrap-image-registry' and click on 'Secrets', inside the 'Workloads' aside menu and then click on 'Create Secret', then select 'Source Secret' and fill with this information

##

##### - Secret Name: github-user
##### - Authentication Type: Basic Authentication
##### - Username: arq-dom-san-digital
##### - Password or Token: Request to the Maintainer juanluis.goldaracena@servexternos.gruposantander.es

##

### Clone GitHub Repository of the Project

##

##### Go to the Terminal Bastion and type this command (You must have the Bitbucket Git Repository Credentials used in last step)
```
git clone https://github.com/arq-dom-san-digital/coinscrap-huchas-digitales-ocp.git
```

##

##### Go to 'configmap' folder of the recent cloned GitHub Repository
```
cd coinscrap-huchas-digitales-ocp
```

##

##### By default, the repositories that have just been cloned leave us positioned in the 'master' branch, which is the one we need to use

##### And check that we are in the desired branch , in this case 'master', using the next command
```
git branch
```
```
cd configmap
```
##

#### Create the ConfigMap 'webapp-env'
```
oc create -f webapp-env-configmap.yaml
```
```
cd ..
```
```
cd template
```

# 2. MAIN Tasks

### Create the Applications of the Project/Namespace 'coinscrap-image-registry' from the 'templates' folder of the cloned Bitbucket Git Repository and logged in project 'coinscrap-image-registry' of the OCP Production Cluster

##

##### The following commands to be executed will be in charge of processing each of the project templates.

##### Each template is associated with each of the applications that will be deployed within the Project/Namespace that we have just created and will be in charge of creating the following OCP Objects / Workloads for each of the applications

##

##### - Build Config

##### - Image Stream

##### - Deployment Config

##### - Replication Controllers

##### - Services

##### - Pods

##

##### They will also be in charge of setting all the environment variables of each one of the applications, making their subsequent modification available from the 'Environment' tab of the 'Deployment Config' in which we find ourselves and want to modify.

##### Every time we make a change in the code repositories that are listening to each of the Builds of the applications that are going to be created with these templates, a new 'Build' will be triggered that will generate a change in the 'ImageStream', which will update our 'Deployment Config' & 'Service' , who in the last step will be in charge of updating the 'Replication Controller' of the application, which will recreate the 'POD's' of the updated application again.

##

#### Create the Application 'webapp'
```
oc process -f build_coinscrap_webapp_cents_template.yaml -p APPLICATION_NAME=webapp \
-p SOURCE_REPOSITORY_URL=https://github.com/arq-dom-san-digital/coinscrap-huchas-digitales-ocp.git \
-p CONTEXT_DIR='webapp/1.0' -p APPLICATION_PORT=3000 -p SOURCE_SECRET=github-user \
-p APPLICATION_TAG=v1.0 -p SOURCE_REPOSITORY_REF=master | oc apply -f-
```

##

### Create the Routes for Consume the Applications

##### Go to the OCP Console and select the Project 'coinscrap-huchas-digitales' and click on 'Routes', inside the 'Networking' aside menu and click on 'Create Route', set the parameters for each Route and click on Create button for each Route

#### Create the Route for 'webapp'
- Name: webapp
- Hostname: coinscrap-webapp-cents.192.168.99.105.nip.io
- Service: webapp
- Target Port: 3000
- Security: Mark the check of 'Secure route'
- TLS termination: Edge



##

### Set Resource Quotas, Limit Ranges & Network Policies

##

##### - CPU Limit Range: 6 Cores (registry=2, registry-ui=1, registry-ui-adm=1, registry-nginx=2)
##### - RAM Limit Range: 6 Gb (registry=2, registry-ui=1, registry-ui-adm=1, registry-nginx=2)
##### - STORAGE Limit Range: 1 Tb (2 Persistent Volumes with 500 Gb)
##### - PODS Limit: 8 Pod's

##

#### Resource Quotas
- Go to the OCP Console and select the Project 'coinscrap-image-registry' and click on 'Resource Quotas', inside the 'Administration' aside menu and then click on 'Create Resource Quota', then copy/paste 'compute-resources.yaml' from the path 'project-config-resources/resource-quotas/' from the repository and then click on 'Create' button
- Repeat the last operation with 'storage-consumption.yaml' file from the path 'project-config-resources/resource-quotas/' from the repository

##

#### Limit Ranges
- Go to the OCP Console and select the Project 'coinscrap-image-registry' and click on 'Limit Ranges', inside the 'Administration' aside menu and then click on 'Create Limit Range', then copy/paste 'core-resource-limits.yaml' from the path 'project-config-resources/limit-ranges/' from the repository and then click on 'Create' button

##

#### Network Policies
- Go to the OCP Console and select the Project 'coinscrap-image-registry' and click on 'Network Policies', inside the 'Networking' aside menu and then click on 'Create Network Policy', then copy/paste 'allow-from-openshift-ingress.yaml' from the path 'project-config-resources/network-policies/' from the repository and then click on 'Create' button
- Repeat the last operation with 'allow-from-openshift-monitoring.yaml' file from the path 'project-config-resources/network-policies/' from the repository
- Repeat the last operation with 'allow-same-namespace.yaml' file from the path 'project-config-resources/network-policies/' from the repository

##


