## YAML Pipeline

In Azure DevOps, Pipelines helps to setup **Continuous Integration/ Continuous Deployment** and to achieve this we have below two option.

1. Classic Editor
2. **YAML Pipeline**

In this Document we will discuss setup YAML Pipeline.

## YAML Pipeline Structure 

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/8e25b884-9a93-46ec-851b-97fd1b71f1c0)

Hierarchy of YAML File

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/0647822a-5fc3-43fd-a22a-0512ae50b13b)

```YAML
stages:
- stage: Build
  jobs:
  - job: BuildPackage
    steps:
    - Build
    - Package
    - Publish
- stage: Deploy
  jobs:
  - job: startDeployment
    steps:
    - Deploypackage
```

## Component of YAML Pipeline

Here is the list of component which we use in YAML pipeline creation.

1. Stages

Stage is collection of Jobs which runs sequentially.

```YAML
stages:
- stage: Build
  jobs:
  - job: BuildOnWindows
    steps:
    - Build
  - job: BuildOnMac
    steps:
    - Build
```

2. Jobs

Job is Collection of Steps that runs on agents/environment.

```YAML
jobs:
- job: BuildPackage
  steps:
  - Build
  - Package
  - Publish
```

3. Steps

Steps helps to define the set of process to setup your task or any activity which you want to perform on any specific job.

Kind of Steps
- Task
- Script 
- templatereference

```YAML
steps:
- Build
- Package
- Publish
```

## Schema of YAML Pipeline file.

```YAML
name: string # Define Custom Build number/Name
variables: #Define variable for Pipeline 
trigger: trigger/none #it defines which branch to enable for CI
stages:[stage| templateReference ]
  jobs:[job| templateReference ]
    steps:[script | task | templateReference ]
```

### Important Note:-

1. In some cases if there is only one stage required for pipeline we can omit stage keyword and can directly start from Jobs.
 
2. In some build setup where only one agent is required for pipeline in that case we can omit Job and directly define the Steps. 

## Demo

We can begin with demo to setup YAML pipeline for below three scenarios.

```diff
- Single Stage, Single Job YAML Pipeline

- Single Stage Multi Job YAML Pipeline

- Multi Stage YAML Pipeline
```

### 1. Single Stage, Single Job YAML Pipeline
   
Assume that if you have assigned to setup build for an application. In this scenario we can omit stages or Jobs.

```yaml
name: sampleWeb_$(Date:yyyyMMdd)$(Rev:.r)
trigger:
- main
pool:
  name: Default
steps:
- task: VSBuild@1
  inputs:
    solution: '**\*.sln'
    msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package 
 /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactstagingdirectory)\\"'
    platform: 'any cpu'
    configuration: 'release'
```

Here you can notice that we did not include the Stage and Job section  in the YAML pipeline as it is not required in this scenario.

### 2. Single Stage Multi Job YAML Pipeline

Let's assume you have to perform some activity on different Agent. Like building application on Windows, Mac OS, Linux. In this scenario we can create multiple job and each job can be assigned with respective agent.

```yaml
name: sampleWeb_$(Date:yyyyMMdd)$(Rev:.r)
trigger:
- main
jobs:
- job: ActivityonLinux
  pool:
   name: default
  steps:
  - script: echo Hello, world!
    displayName: 'Run a one-line script'
- job: ActivityonWindows
  pool:
   name: Default
  steps:
   - task: PowerShell@2
     inputs:
       targetType: 'inline'
       script: |
         # Write your PowerShell commands here.
         
         Write-Host "Hello World"
```

### 3. Multi Stage YAML Pipeline.
   
In this scenario you have assigned one application where you need to build the Project, package it. After that you need to deploy the application. In this case we can create two stage.

Stage1 --> Build the Application

Stage 2 --> Deploy the Application

```yaml
name: sampleApp_$(Date:yyyyMMdd)$(Rev:.r)
trigger:
- main
stages:
- stage: Build
  jobs:
  - job: Build
    pool:
     name: Default
    workspace:
     clean: outputs 
    steps:
    - task: VSBuild@1
      inputs:
        solution: '**\*.sln'
        msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactstagingdirectory)\\"'
        platform: 'any cpu'
        configuration: 'release'
    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: 'drop'
        publishLocation: 'Container'
- stage: Deploy
  jobs:
  - job: DeployWeb
    pool:
      name: Default
    steps:
    - powershell: "write-host"
    - task: DownloadBuildArtifacts@1
      inputs:
        buildType: 'specific'
        project: '0a1af395-65ea-473b-b60e-01fdc0d3f93e'
        pipeline: '11'
        buildVersionToDownload: 'latest'
        downloadType: 'single'
        downloadPath: '$(System.ArtifactsDirectory)'
    - task: AzureRmWebAppDeployment@4
      inputs:
        ConnectionType: 'AzureRM'
        azureSubscription: 'testconn'
        appType: 'webApp'
        WebAppName: 'demotest0103'
        packageForLinux: '$(System.ArtifactsDirectory)/**/*.zip'
```

## What is template in ADO YAML

Template in general helps to cerate some sharable content can be used by many team or in Many project.


With the help of Template we can define sharable content, logic and parameters in YAML pipeline.

## Why ADO YAML template

- Speed up development
- secure pipeline
- cleaner yaml pipeline
- avoid same logic to add in multiple place 

Types of templates

1. Include --> insert reusable control

2. Extends --> Insert what is allowed. define logic that other template file follow.

### Templates category

- stage template
- job template
- step template
- variable template

  ![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/82505c5f-f149-4c4f-af81-c04c2132eba0)

### step template syntax

Now lets understand how we can create step template and how it can be referred from another yaml pipeline file.

step template helps to group all the steps that can be sharable in many jobs or stages.
![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/0efd259f-d5e5-450c-b699-570003db35dc)


### job template syntax

job template helps to group all the Jobs that can be sharable in many jobs and stages.

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/41495bed-d772-4c18-ba55-3ebcc88b19a2)


### stage template syntax

stage template helps to group all the stages that can be sharable reference in many master pipeline.

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/a5c811c3-ed84-4f9f-975f-5153399b07dd)


### variable template syntax

variable template helps to group all the variables that can be referenced in pipeline.

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/bf37663b-20d0-4b35-a510-4d8dbc3d5150)


## Create template with parameters

In some scenario you are required to pass some parameters to the template. We can create parameters with all types of template like step, stage, jobs, variables.


In this case we need to declare and define the parameters in two files.

1. In template file 
2. the file from which template file is being called.

example:

### Job Template with parameters example

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/a90db234-c140-49c2-ac30-304418d227d1)


### Steps Template with parameters example

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/54eccfe6-226d-4c77-909a-54af20745f94)

### Variables Template with parameters example

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/0f9464f4-0624-4cec-ab5d-e2e1714ee84a)

## Real Scenario example

![image](https://github.com/divyanshursahu/AzureDevOps/assets/96013623/2839c655-159c-4ff6-871b-1eb4cb3e2d34)

Lets assume you want to setup of CI/CD pipeline for an Application and to better manager the pipeline you want to make use of template.

Items:

1. master pipeline
2. stage template
3. jobs template 
4. steps template

Reference: 

[complete-guide-to-learn-and-setup-yaml-pipeline-in-azure-devops](https://www.letsdevops.net/post/letsdevops-complete-guide-to-learn-and-setup-yaml-pipeline-in-azure-devops)

[templates-how-and-when-to-create-template-with-real-scenario-example](https://www.letsdevops.net/post/letsdevops-azure-devops-yaml-templates-how-and-when-to-create-template-with-real-scenario-example)
