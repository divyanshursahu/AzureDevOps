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

## Important Note:-

