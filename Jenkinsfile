pipeline{
    agent any
    stages{
        stage("Git"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'GITHUB', passwordVariable: 'GIT_PASS', usernameVariable: 'GIT_USER')]){
                    sh "git clone https://${GIT_USER}:${GIT_PASS}@github.com/MartiMarch/formacion-jenkins-caso-de-uso-real.git"
                }
            }
        }
        stage("Maven"){
            steps{
                script{
                    sh "ls -ll"
                    sh "chdir=${WORKSPACE}/formacion-jenkins-caso-de-uso-real mvn validate compile test package verify install"
                }
            }
        }
    }
}
