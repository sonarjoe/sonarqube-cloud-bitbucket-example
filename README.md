## Overview

This project is an example of using Bitbucket pipelines for a python project. It demonstrates how to set up a CI/CD pipeline for a Python application.  

PLEASE READ OUR SONARQUBE DOCUMENTATION FOR WORKING WITH BITBUCKET PIPELINES  
[Bitbucket Cloud - SonarQube Server Integration](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/bitbucket-integration/bitbucket-cloud-integration/)  
[Bitbucket Server and Bitbucket Data Center - SonarQube Server Integration](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/bitbucket-integration/bitbucket-server-integration/)  
[Bitbucket Pipelines - SonarQube Cloud](https://docs.sonarsource.com/sonarqube-cloud/advanced-setup/ci-based-analysis/bitbucket-pipelines-for-sonarcloud/)


## Important Information in Pipelines 
- The pipeline is configured to run only under the following conditions:  
  - When the branch is `main`.  
  - When the pipeline is triggered by a pull request event.  
- We are using the `sonarqube-scan` or `sonarcloud-scan` pipe to execute the SonarScanner in your Bitbucket pipeline.  
- The `SONAR_TOKEN` and `SONAR_HOST_URL` variables are defined in the **Repository Variables** section in Bitbucket. 
- For more information on how to limit your analysis scope and parameters available, please check **SonarScanner Analysis Scope** and **SonarScanner Analysis Parameters** in the Important Links section.

## Important Links 
[Bitbucket Cloud - SonarQube Server Integration](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/bitbucket-integration/bitbucket-cloud-integration/)  
[Bitbucket Server and Bitbucket Data Center - SonarQube Server Integration](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/bitbucket-integration/bitbucket-server-integration/)  
[Bitbucket Pipelines - SonarQube Cloud](https://docs.sonarsource.com/sonarqube-cloud/advanced-setup/ci-based-analysis/bitbucket-pipelines-for-sonarcloud/)  
[Bitbucket setting repository variables](https://docs.sonarsource.com/sonarqube-server/latest/devops-platform-integration/bitbucket-integration/bitbucket-cloud-integration/#setting-environment-variables)  
[Bitbucket pipes | sonarqube-scan](https://bitbucket.org/sonarsource/sonarqube-scan/src/master/)
[Bitbucket pipes | sonarqube-quality-gate](https://bitbucket.org/sonarsource/sonarqube-quality-gate/src/master/)
[Bitbucket pipes | sonarcloud-scan](https://bitbucket.org/sonarsource/sonarcloud-scan/src/master/)
[Bitbucket pipes | sonarcloud-quality-gate](https://bitbucket.org/sonarsource/sonarcloud-quality-gate/src/master/)
[SonarScanner Analysis Scope](https://docs.sonarsource.com/sonarqube-server/latest/project-administration/analysis-scope/)  
[SonarScanner Analysis Parameters](https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/analysis-parameters/)  

## Example to fail the entire pipeline if Quality Gate fails
There may be situations or branches in which you will like to fail the pipeline if the SonarQube Quality Gate fails in order to stop any other steps in the pipeline.  
This can be done in two ways.
First, and recommended, is by adding the `sonarqube-quality-gate` pipe to your pipeline. This pipe will check the result of the analysis and will fail the pipeline if the Quality Gate fails. 

Example
``` sh
   - step: &sonarqube-quality-gate
        name: Check Quality Gate on SonarQube
        max-time: 5 
        script:
          - pipe: sonarsource/sonarcloud-quality-gate:0.2.0
            variables:
              SONAR_TOKEN: ${SONAR_TOKEN}
```


Second by adding `sonar.qualitygate.wait=true` to the `SONAR_EXTRA_PARAMS` section in the `sonarqube-scan/sonarcloud-scan` pipe.  

Example
``` sh
     - step: &sonarqube-analysis
        name: SonarQube analysis
        caches:
          - sonar
      - pipe: sonarsource/sonarcloud-scan:4.1.0
          variables:
             SONAR_HOST_URL: ${SONAR_HOST_URL}
             SONAR_TOKEN: ${SONAR_TOKEN}
             EXTRA_ARGS: ["-Dsonar.organization=sonarqube-workspace-example -Dsonar.qualitygate.wait=true -Dsonar.qualitygate.timeout=300"]
             DEBUG: "true"
```