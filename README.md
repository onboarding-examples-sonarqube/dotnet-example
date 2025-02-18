## Overview

This repository is an example of setting up the SonarScanner Analysis with GitHub Actions pipeline for a csharp/dotnetcore project.  
We have multiple CI/CD Pipeline examples, one for connecting to SonarQube Server instance and the other to SonarQube Cloud instance.   

__**PLEASE READ OUR SONARQUBE DOCUMENTATION FOR WORKING WITH GITHUB ACTIONS PIPELINES**__  
[GitHub Actions Workflow with SonarQube Scanner](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/github-integration/adding-analysis-to-github-actions-workflow/)  

## Important Information in Pipelines
- On triggers are set to only execute on changes to specific branches and a specific directory in the project, this can be modified with whatever trigger you would want to use.
- They have shallow fetch set to 0. this is required for SonarScanner to properly analyze your project.  
- For more information on how to limit your analysis scope and parameters available, please check **SonarScanner Analysis Scope** and **SonarScanner Analysis Parameters** in the Important Links section.
- The action used for SonarScanner Analysis is executed via the dotnetcore command, which applies for both SonarQube Server and SonarQube Cloud. But they require different parameters. Examples for both are provided.
    - SonarQube Cloud Example: sonarqube-cloud.yml  
    - SonarQube Server Example: sonarqube-server.yml 
- For both `projectKey (/k)` and `projectName (/n)`, we are using the following `$(echo ${{ github.repository }} | cut -d'/' -f1)-gh_$(echo ${{ github.repository }}` as naming convention. This results in `OrgName-gh_RepoName`.  
- Please make sure you have set up your `SONAR_TOKEN` and `SONAR_HOST_URL` secrets or variables. In the command used, `SONAR_TOKEN` is set up as a secret and `SONAR_HOST_URL` is set a variable. If set up differently please change the prefix in the respective parameter.   

## Important Links
[SonarQube Server - GitHub Integration](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/github-integration/introduction/)  
[SonarQube Cloud - GitHub Integration](https://docs.sonarsource.com/sonarqube-cloud/getting-started/github/)  
[SonarScanner Analysis Scope](https://docs.sonarsource.com/sonarqube-server/latest/project-administration/analysis-scope/)  
[SonarScanner Analysis Parameters](https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/analysis-parameters/)   
[SonarScanner for .NET](https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/scanners/dotnet/introduction/)  

## Example to fail the entire pipeline if Quality Gate fails
In certain situations, you may want to halt/fail the pipeline if the SonarQube Quality Gate fails, preventing subsequent steps from executing.  
This can be achieved by adding `sonar.qualitygate.wait=true` to the parameters section in the `dotnet-sonarscanner begin` command.  

Example:
``` sh
    .\.sonar\scanner\dotnet-sonarscanner begin /k:$(echo ${{ github.repository }} | cut -d'/' -f1)-gh_$(echo ${{ github.repository }} | cut -d'/' -f2) /n:$(echo ${{ github.repository }} | cut -d'/' -f1)-gh_$(echo ${{ github.repository }} | cut -d'/' -f2) /d:sonar.token="${{ secrets.SONAR_TOKEN }}" /d:sonar.host.url="${{ vars.SONAR_HOST_URL }}" /d:sonar.verbose=true /d:sonar.qualitygate.wait=true
    dotnet build CSharpProject/SomeConsoleApplication.sln
    .\.sonar\scanner\dotnet-sonarscanner end /d:sonar.token="${{ secrets.SONAR_TOKEN }}"
          
```