# dotnet 6 console app

git init
git add -A
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/xxx/dotnet6myconsole.git
git push -u origin main
git remote add azure https://xxx@dev.azure.com/xxx/dotnet6myapp/_git/dotnet6myconsole
git push -u azure main


Azure pipeline:
# ASP.NET Core
# Build and test ASP.NET Core projects targeting .NET Core.
# Add steps that run tests, create a NuGet package, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/dotnet-core

trigger:
- main

pool:
  vmImage: ubuntu-latest

variables:
  buildConfiguration: 'Release'

steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)' 


- task: DotNetCoreCLI@2
  inputs:
    command: publish 
    arguments: --configuration $(BuildConfiguration) --self-contained -r win10-x64 --output $(build.artifactstagingdirectory)
    projects: '**/*.csproj'
    publishWebProjects: false
    zipAfterPublish: false
  enabled: true

- task: PublishBuildArtifacts@1
  inputs:
    pathtoPublish: '$(Build.ArtifactStagingDirectory)' 
    artifactName: 'dotnet6myconsole'



release task:
Azure PowerShell script: InlineScript

# You can write your azure powershell scripts inline here. 
# You can also pass predefined and custom variables to this script using arguments
cd $(System.DefaultWorkingDirectory)
ls
cd _dotnet6myconsole/dotnet6myconsole
ls
cd APP
.\APP.exe
 


release pipeline notes:
https://subhankarsarkar.com/dotnet-core-webjob-console-app-ci-cd-using-azure-devops-pipelines/
https://devopsjournal.io/blog/2019/08/17/AzureDevOps-Run-NET-Core

steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)' 


- task: DotNetCoreCLI@2
  inputs:
    command: publish 
    arguments: -r win-x64 -p:PublishSingleFile=True --self-contained false -o $(Build.ArtifactStagingDirectory)
    projects: '**/*.csproj'
    publishWebProjects: false
    zipAfterPublish: True
  enabled: true

- task: PublishBuildArtifacts@1
  inputs:
    pathtoPublish: '$(Build.ArtifactStagingDirectory)' 
    artifactName: 'dotnet6myconsole'

---------------------------


steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)' 

- task: CopyFiles@2
  inputs:
    targetFolder: '$(Build.ArtifactStagingDirectory)' 
   
- task: ArchiveFiles@2
  inputs:
    rootFolderOrFile: '$(Build.BinariesDirectory)'
    includeRootFolder: false
    archiveType: 'zip'
    archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
    replaceExistingArchive: true

- task: PublishBuildArtifacts@1    
  displayName: 'Publish Artifact: drop'
  inputs:
    PathtoPublish: '$(build.artifactstagingdirectory)'


-------------------------------


steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)' 

- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration $(BuildConfiguration) --output $(Build.ArtifactStagingDirectory)'
    zipAfterPublish: True

- task: PublishBuildArtifacts@1
  inputs:
    pathtoPublish: '$(Build.ArtifactStagingDirectory)' 
    artifactName: 'dotnet6myconsole'

 -------------------

 steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)' 


- task: DotNetCoreCLI@2
  inputs:
    command: publish 
    arguments: -r win-x64 -p:PublishSingleFile=True --self-contained false -o $(Build.ArtifactStagingDirectory)
    projects: '**/*.csproj'
    publishWebProjects: false
    zipAfterPublish: false
  enabled: true

- task: PublishBuildArtifacts@1
  inputs:
    pathtoPublish: '$(Build.ArtifactStagingDirectory)' 
    artifactName: 'dotnet6myconsole'