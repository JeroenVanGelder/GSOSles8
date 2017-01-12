def serviceName
def serviceScaleInt

node('master') {
    def buildImage = "swarmhost.ccveu.local:5000/mvn-swarm-node:1.0"
    stage('Swarm preparation'){
        def services = sh(returnStdout: true, script: "Docker service ls | cut -d ' ' -f12")
        echo services + ' test'
        def valid = services.contains(buildImage)
        if(valid == true)        {
            serviceName = sh(returnStdout: true, script: "Docker service ls | grep $buildImage | cut -d ' ' -f3")
            def serviceScaleString = sh(returnStdout: true, script: "Docker service ls | grep mvn | cut -d ' ' -f5 | cut -d '/' -f2")
            serviceScaleInt = serviceScaleString as int
            serviceScaleInt ++
            serviceName = serviceName.replaceAll("[\r\n]+", "");
            sh "Docker service scale $serviceName=$serviceScaleInt"
        }
        else{
            serviceName = sh(returnStdout: true, script: "Docker service create --constraint 'node.role != manager' --replicas 1 $buildImage")
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
            sh "Docker service rm $serviceName"
        }
        else{
            echo serviceName + " | " + serviceScaleInt
            serviceScaleInt --
            sh "Docker service scale $serviceName=$serviceScaleInt"
        }
    }
}

