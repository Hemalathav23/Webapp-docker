node{
    
   def buildNumber = BUILD_NUMBER
   
    stage("Git CheckOut"){
        git url: 'https://github.com/Hemalathav23/web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
    } 
    
    stage("Build Dokcer Image") {
         sh "docker build -t Hemalathav23/web-app-docker:${buildNumber} ."
    }
    
    
    stage("Docker Push"){
        
       withCredentials([string(credentialsId: 'Docker_Repo_Pwd', variable: 'Docker_Repo_Pwd')]) {
          sh "docker login -u admin -p ${Docker_Repo_Pwd}  Hemalathav23"   
       }
       
        sh "docker push Hemalathav23/web-app-docker:${buildNumber}"
    }

   stage("Deploy to docker swarm cluster"){
     sshagent(['Docker_Swarm_Manager_Dev']) {
      sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.10.56 docker service rm javawebapp || true'
      withCredentials([string(credentialsId: 'Docker_Repo_Pwd', variable: 'Docker_Repo_Pwd')]) {
 
       sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.10.56 docker login -u admin -p ${Docker_Repo_Pwd}  web-app-docker"   
      }
      sh "ssh ubuntu@172.31.10.56 docker service create --name javawebapp -p 7070:8080 --replicas 2 --with-registry-auth 172.31.1.204:8083/java-web-app:${buildNumber}"
    }
  }
    
}
