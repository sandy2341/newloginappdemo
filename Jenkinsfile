pipeline {
  agent any
  tools {
  maven 'maven'
  }
    stages {

  stage ('Checkout SCM'){
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/sandy2341/newloginappdemo.git']]])
      }
   }
	  
	stage ('Build')  {
	    steps {
        dir('loginappDemo'){
            sh "mvn package"
          }
        }    
   }
   
  stage ('SonarQube Analysis') {
    steps {
      withSonarQubeEnv('sonar') {           
				dir('loginappDemo'){
          sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=Sonar_Demo1'
        }
    }
    }
 }
     stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "JFrog",
                    url: "https://sandya.jfrog.io/artifactory",
                    credentialsId: "JFrog"
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "jfrog",
                    releaseRepo: "newloginappdemo-libs-release-local",
                    snapshotRepo: "newloginappdemo-libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "jfrog",
                    releaseRepo: "default-maven-virtual",
                    snapshotRepo: "default-maven-virtual"
                )
            }
    }

    stage ('Deploy Artifacts') {
            steps {
                rtMavenRun (
                    tool: "maven", // Tool name from Jenkins configuration
                    pom: 'loginappDemo/pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
         }
    }

  } 
}
