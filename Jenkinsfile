node{
    stage('Checkout'){
        git 'https://github.com/gchakra9/insurance-project-demo.git'
    }
    stage('Maven Build'){
        sh 'mvn clean package'
    }
    stage('Containerize'){
        sh 'docker build -t gouravchakraborty/insure-me:1.0 .'
    }
    stage('Release to Docker Hub'){
        withCredentials([string(credentialsId: 'DockerHubPassword', variable: 'DockerHubPassword')]) {
            sh "docker login -u gouravchakraborty -p ${DockerHubPassword}"
           sh 'docker push gouravchakraborty/insure-me:1.0' 
        }
    }
    stage('Deploy to Test Server'){
        ansiblePlaybook become: true, credentialsId: 'ansible-ssh-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-test-server.yml', vaultTmpPath: ''
    }
    stage('Checkout Regression Test code'){
        git 'https://github.com/gchakra9/my-selenium-test-app.git'
    }
    stage('Build Test Script'){
        sh 'mvn clean package assembly:single'
    }
    stage('Execute Selenium Test on Test Server'){
        sh 'java -jar target/my-app-test-0.0.1-SNAPSHOT-jar-with-dependencies.jar'   
    }
    stage('Checkout for prod deploy'){
        git 'https://github.com/gchakra9/insurance-project-demo.git'
    }
    stage('Deploy to Test Server'){
        ansiblePlaybook become: true, credentialsId: 'ansible-ssh-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-prod-server.yml', vaultTmpPath: ''
    }
    
}
