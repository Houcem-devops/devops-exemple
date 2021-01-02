node {
    // reference to maven
    // ** NOTE: This 'maven-3.5.2' Maven tool must be configured in the Jenkins Global Configuration.   
    //def mvnHome = tool 'maven-3.5.2'
    def mvnHome = tool (name: 'maven', type: 'maven')

    // holds reference to docker image
    def dockerImage
    // ip address of the docker private repository(nexus)
 
    def dockerImageTag = "houcemserver/sp"
    
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
       git credentialsId: 'rootgit', url: 'git@gitlab.com:houcemdevops/devops-example.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.5.2' Maven tool must be configured
      // **       in the global configuration.           
      //mvnHome = tool 'maven-3.5.2'
    }    
    
   stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' clean install"
    }
		
    stage('Build Docker Image') {
      // build docker image
      dockerImage = docker.build("houcemserver/sp")
    }
    
    stage("Docker Push"){
    withCredentials([string(credentialsId: 'secret', variable: 'secret')]) {
    sh "docker login -u houcemserver -p ${secret}"
    }
    sh "docker push ${dockerImageTag}"
    }
    
    stage("Deploy in k8s"){
     sshPublisher(publishers: [sshPublisherDesc(configName: 'k8s_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f deployment.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'deployment.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
    }
}
