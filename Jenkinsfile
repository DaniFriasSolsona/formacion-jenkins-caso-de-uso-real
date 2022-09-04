pipeline{
    agent any
    stages{
        stage("Git"){
            steps{
                sh "rm -rf ./*"
                withCredentials([usernamePassword(credentialsId: 'GITHUB', passwordVariable: 'GIT_PASS', usernameVariable: 'GIT_USER')]){
                    sh "git clone https://${GIT_USER}:${GIT_PASS}@github.com/MartiMarch/formacion-jenkins-caso-de-uso-real.git"
                }
            }
        }
        stage("Maven"){
            steps{
                script{
                    sh "chdir=${WORKSPACE}/formacion-jenkins-caso-de-uso-real/ mvn validate compile test package verify install"
                }
            }
        }
        stage("Docker"){
            steps{
                script{
                    def jarName = sh(returnStdout: true, script: "chdir=${WORKSPACE} find -name *.jar*")
                    jarName = jarName.split("/")
                    jarName = jarName[jarName.size()]
                    def jarPath = "chdir=${WORKSPACE}/formacion-jenkins-caso-de-uso-real/target"
                }
            }
        }
    }
}
