node{
 properties(
    [
        parameters(
            [string(defaultValue: 'tpmgnew', name: 'project'),
             string(defaultValue: 'my-first-microservice', name: 'appName'),
             string(defaultValue: 'development', name: 'imageVersion'),
             string(defaultValue: '1', name: 'replica'),
             string(defaultValue: 'development', name: 'namespace')]
            )

    ]
    )    
  //Define all variables
  //def project = 'tpmgnew'
  //def appName = 'my-first-microservice'
  //def serviceName = "${appName}-backend"  
  //def imageVersion = 'development'
  //def namespace = 'development'
  def imageTag = "anandjain420/${project}:${imageVersion}.${env.BUILD_NUMBER}"
  
  //Checkout Code from Git
  checkout scm
  
  //Stage 1 : Build the docker image.
  stage('Build image') {
      sh("docker build -t ${imageTag} .")
  }
  
  //Stage 2 : Push the image to docker registry
  stage('Push image to registry') {
      sh("docker -- push ${imageTag}")
  }
  
  //Stage 3 : Deploy Application
  stage('Deploy Application') {
       switch (namespace) {
              //Roll out to Dev Environment
              case "development":
                   // Create namespace if it doesn't exist
                   sh("kubectl get ns ${namespace} || kubectl create ns ${namespace}")
           //Update the imagetag to the latest version
                   //sh("sed -i.bak 's#anandjain420/${project}:${imageVersion}#${imageTag}#' ./k8s/development/*.yaml")
                   //s/image:/image: anandjain420\/anandjain420\/tpmgnew:development.10/g
                   //sh("sed -i.bak 's/image:/image: ${imageTag}/g' ./k8s/development/*.yaml")
                   sh("sed -i.bak 's#replicas:*#replicas: ${replica}#' ./k8s/development/*.yaml")
                   sh ("echo 'one'")
                   sh("sed -i.bak 's#anandjain420/${project}:${imageVersion}#${imageTag}#' ./k8s/development/*.yaml")
                   //Create or update resources
           sh("kubectl --namespace=${namespace} apply -f k8s/development/deployment.yaml")
                   sh("kubectl --namespace=${namespace} apply -f k8s/development/service.yaml")
           //Grab the external Ip address of the service
                   break
           
        //Roll out to Dev Environment
              case "production":
                   // Create namespace if it doesn't exist
                   sh("kubectl get ns ${namespace} || kubectl create ns ${namespace}")
           //Update the imagetag to the latest version
                   sh("sed -i.bak 's#anandjain420/${project}:${imageVersion}#${imageTag}#' ./k8s/production/*.yaml")
           //Create or update resources
                   sh("kubectl --namespace=${namespace} apply -f k8s/production/deployment.yaml")
                   sh("kubectl --namespace=${namespace} apply -f k8s/production/service.yaml")
           //Grab the external Ip address of the service
                   break
       
              default:
                   sh("kubectl get ns ${namespace} || kubectl create ns ${namespace}")
                   sh("sed -i.bak 's#anandjain420/${project}:${imageVersion}#${imageTag}#' ./k8s/development/*.yaml")
                   sh("kubectl --namespace=${namespace} apply -f k8s/development/deployment.yaml")
                   sh("kubectl --namespace=${namespace} apply -f k8s/development/service.yaml")
                   break
  }
}
}
