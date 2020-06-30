#!/usr/bin/env groovy
pipeline {
  agent any
  tools {nodejs "PipelineNode"}
  stages {
    stage('Install VBT and SFDX-CLI') {
      steps {
      	// Step to Install and Setup VBT and SFDX-CLI
		  sh 'node -v'
		  sh 'npm install -g sfdx-cli'
      sh 'echo "y" |sfdx plugins:install datapacks'
      }
    }
    stage('SFDX-Auth') {
      steps {
      	// creating SFDX Alias for auth
		    sh 'echo ${SFDX_URL} > env.sfdx'
		    sh 'sfdx force:auth:sfdxurl:store -d -a ALIAS -f env.sfdx'
		    sh 'rm -rf env.sfdx'
		    sh 'sfdx force:org:display -u ALIAS'
      }
    }
    stage('SF Deploy') {
      steps {
      	// SF Metadata Deploy
		    sh 'sfdx force:source:deploy --sourcepath salesforce_sfdx --targetusername ALIAS --verbose'
      }
    }
    stage('DataPacks Deploy') {
      steps {
      	// DP Deploy
		    sh 'sfdx datapacks:deploy --logging --targetusername ALIAS  --all'
        // Apex Post Deplyment Jobs (Optional)
		    sh 'sfdx force:apex:execute --targetusername ALIAS --apexcodefile ./apex/RunProductBatchJobs.cls'
      }
    }
  }  
}