def serviceName
def serviceScaleInt

node('master') {
    def buildImage = "swarmhost.ccveu.local:5000/mvn-swarm-node:1.0"
    
    sh "docker info"
    stage('Swarm preparation'){
        def services = sh(returnStdout: true, script: "docker service ls | cut -d ' ' -f12")
        echo services + ' test'
        def valid = services.contains(buildImage)
        if(valid == true)        {
            serviceName = sh(returnStdout: true, script: "docker service ls | grep $buildImage | cut -d ' ' -f3")
            def serviceScaleString = sh(returnStdout: true, script: "docker service ls | grep mvn | cut -d ' ' -f5 | cut -d '/' -f2")
            serviceScaleInt = serviceScaleString as int
            serviceScaleInt ++
            serviceName = serviceName.replaceAll("[\r\n]+", "");
            sh "docker service scale $serviceName=$serviceScaleInt"
        }
        else{
            serviceName = sh(returnStdout: true, script: "docker service create -e JENKINS_SERVER=${JenkinsIP} -e JENKINS_PORT=${JenkinsPort} --constraint 'node.role != manager' --replicas 1 $buildImage")
        }
    }
}

node('maven && swarm'){
    def mvnHome
    stage ('Build'){
        git 'https://github.com/JeroenVanGelder/GSOSles8.git'
        sh 'mvn --version'
    }
}

node('master'){
    stage ('scale down'){
        
        if(serviceScaleInt == null) {
            echo serviceName + " -- "
            sh "docker service rm $serviceName"
        }
        else{
            echo serviceName + " | " + serviceScaleInt
            serviceScaleInt --
            sh "docker service scale $serviceName=$serviceScaleInt"
        }
    }
}

