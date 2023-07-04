pipeline {
    agent {
        node{
        label 'maven-server'
        }
    }
    
    tools {
        maven 'mvn'
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git credentialsId: 'ps', url: "${Repo}"

            }
        }
        stage ('Build'){
            steps{
                script{
                    sh "mvn clean install"
                }
            }
        }
		stage('Sonar'){
            steps{
                script {
                 withSonarQubeEnv(credentialsId: 'sonarqube') {
                 sh 'mvn sonar:sonar -Dsonar.projectName=test -Dsonar.projectKey=test'
                }
                }
            }
        }
	    
		stage('Uploading to JFrog Artifactory') {
			steps{
			rtUpload(
			serverId: "jfrog",
			spec: '''{
			"files":[{
			"pattern": "module-a/target/*.jar",
			"target": "Test-Repo"
			}]
			}
			''',
			)
			}
		}
		stage('Archieving the Artifact'){
		steps{
		archiveArtifacts artifacts: 'module-a/target/*.jar', followSymlinks: false
		}
		}
    }
}
