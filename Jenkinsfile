podTemplate(label: 'jenkins-slave', cloud: 'kubernetes', containers: [
    containerTemplate(
        name: 'jnlp', 
        image: "jenkins-salve:aliang"
    ),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker'),
    hostPathVolume(mountPath: '/home/jenkins/.kube', hostPath: '/root/.kube'),
    hostPathVolume(mountPath: '/usr/bin/kubectl', hostPath: '/usr/bin/kubectl'),
  ],
) 
{
  node("jenkins-slave"){
      // 第一步
      stage('拉取代码'){
         checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/lvsir625/trucks.git']]])
      }
      // 第二步
      stage('代码编译'){
          sh "cd trucks"
          sh "mvn clean package -Dmaven.test.skip=true"
      }
      """
      // 第三步
      stage('构建镜像'){
          withCredentials([usernamePassword(credentialsId: "${docker_registry_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {
            sh """
              echo '
                FROM lizhenliang/tomcat 
                RUN rm -rf /usr/local/tomcat/webapps/*
                ADD target/*.war /usr/local/tomcat/webapps/ROOT.war 
              ' > Dockerfile
              ls 
              ls target
              docker build -t ${image_name} .
              docker login -u ${username} -p '${password}' ${registry}
              docker push ${image_name}
            """
            }
      }
      // 第四步
      stage('部署到K8S平台'){
          sh """
          sed -i 's#\$IMAGE_NAME#${image_name}#' deploy.yml
          sed -i 's#\$SECRET_NAME#${secret_name}#' deploy.yml
          """
          kubernetesDeploy configs: 'deploy.yml', kubeconfigId: "${k8s_auth}"
      }
      """
  }
}
