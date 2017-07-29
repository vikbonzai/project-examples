pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                script {
                    git url: 'https://github.com/jbaruch/project-examples'
                    server = Artifactory.server ARTIFACTORY
                    buildInfo = Artifactory.newBuildInfo()
                    buildInfo.env.capture = true
                    rtMaven = Artifactory.newMavenBuild()
                    rtMaven.tool = MAVEN
                    rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'
                    rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
                }
            }
        }
        stage('Cleanup') {
          steps {
              script {
                if(CLEAN_REPO) {
                    echo 'Cleaning local repo'
                    sh 'rm -rf ~/.m2/repository'
                }
             }
          }
        }
        stage('Build') {
            steps {
                script {
                    rtMaven.run pom: 'maven-example/pom.xml', goals: '-B clean install', buildInfo: buildInfo
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    rtMaven.deployer.deployArtifacts buildInfo
                }
            } 
        }
    }
}
