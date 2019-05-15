podTemplate(label: 'jnlp-slave', cloud: 'kubernetes', containers: [
    containerTemplate(
        name: 'jnlp', 
        image: '118.25.127.51:8088/jenkins-slave/jenkins-slave:latest', 
        alwaysPullImage: true 
    ),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker'),
    hostPathVolume(mountPath: '/usr/local/jdk', hostPath: '/usr/local/jdk'),
    hostPathVolume(mountPath: '/usr/local/maven', hostPath: '/usr/local/maven'),
  ],
  imagePullSecrets: ['registry-pull-secret'],
) 
{
  node("jnlp-slave"){
      stage('Git Checkout'){
         checkout([$class: 'GitSCM', branches: [[name: '${Tag}']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '6422871b-528f-4250-8aac-44d6db137ca5', url: 'https://github.com/samhu1234/solo.git']]])
      }
      stage('Unit Testing'){
      	echo "Unit Testing..."
      }
      stage('Maven Build'){
          sh "mvn clean package -Dmaven.test.skip=true"
      }
      stage('Build and Push Image'){
          sh '''
          docker login -u samhu1234 -p aimeesam1955 118.25.127.51:8088
          docker build -t 118.25.127.51:8088/project/solo:${Tag} -f deploy/Dockerfile .
          docker push 118.25.127.51:8088/project/solo:${Tag}
          '''
      }
      stage('Deploy to K8S'){
          sh '''
          cd deploy 
          sed -i "/118.25.127.51:8088/{s/latest/${Tag}/}" deploy.yaml
          sed -i "s/environment/${Env}/" deploy.yaml
          ''' 
          kubernetesDeploy configs: 'deploy/deploy.yaml', kubeConfig: [path: ''], kubeconfigId: '42bc3964-f055-49cb-b9e2-f30ccc1fa850', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
      }
      stage('Testing'){
          echo "Testing..."
      }
  }
}

