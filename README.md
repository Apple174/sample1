node{
    def MavenHome= tool name: "maven"
    def build = BUILD_NUMBER
    stage(' git clone ')
    {
        git credentialsId: '79e8849f-fa84-4d70-9625-8557af8c0bac', url: 'https://github.com/Alone174/java-web-app-docker.git'
    }
    stage('maven part')
    {
        sh "${MavenHome}/bin/mvn clean package"
        
        
    }
    stage('docker buid')
    {
        sh "docker build -t ram174/java-web-app:6 ."
    }
    stage(' docker push ')
    {
        sh "docker login -u ram174 -p R8790889591r@#"
        sh "docker push ram174/java-web-app:6"
    }
    stage (' deploy docker as container ')
    {
       sshagent(['docke_con']) {
           sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.14.227 docker rm -f javawepapp || true"
           sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.14.227 docker run -d -p 8080:8080 --name javawebapp ram174/java-web-app:6"
        }
        
    }
}
