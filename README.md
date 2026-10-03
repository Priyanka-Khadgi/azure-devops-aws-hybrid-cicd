# Azure DevOps–AWS Hybrid CI/CD

A YAML-based CI/CD pipeline implementation that uses **Azure DevOps as the CI/CD orchestration layer** to build and package a Java web application and distribute the resulting WAR artifact across **Azure DevOps Artifacts, Amazon S3, and Azure Blob Storage**.

A code push to the `main` branch triggers the pipeline on a **Linux-based self-hosted Azure DevOps agent hosted on an AWS EC2 instance**. The pipeline installs and verifies Java, executes the build script to compile the application and package it as a WAR file, and versions the generated artifact using the Azure DevOps build ID. For example, `ROOT.war` becomes `ROOT125.war` for build `125`.

The versioned WAR is copied to the Azure DevOps artifact staging directory and published as a build artifact. The same artifact is then uploaded to **Amazon S3 and Azure Blob Storage**, providing a single CI/CD workflow for artifact distribution across both AWS and Azure.

```text
Git Push
   │
   ▼
Azure DevOps Pipeline
   │
   ▼
Linux Self-Hosted Agent
   │
   ├── Install / Verify Java
   ├── Compile Java Sources
   └── Package → ROOT.war
              │
              ▼
       ROOT<BuildId>.war
              │
       ┌──────┼──────────┐
       ▼      ▼          ▼
    Azure    Amazon    Azure Blob
    DevOps    S3        Storage
   Artifact
```

### Authentication

AWS and Azure access is handled through **Azure DevOps service connections**. The pipeline references the configured service connections for AWS and Azure operations instead of storing access keys, passwords, or other credentials in the YAML file. This keeps cloud credentials external to the source code while allowing the pipeline to authenticate with the required services during execution.

<img width="1554" height="649" alt="AWS+Azure+AzureDevOps+Linux" src="https://github.com/user-attachments/assets/6b34f218-88fc-48c7-8a76-f683d5a2964d" />
