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
          sh "sleep 100"
          sh "mvn clean package -Dmaven.test.skip=true"
      }
      
  }
}
