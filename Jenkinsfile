pipeline{
    agent any
    stages{
        stage("Git"){
            steps{
                git url:"https://github.com/MartiMarch/formacion-jenkins-groovy.git"
            }
        }
        stage("Maven"){
            steps{
                script{
                    sh "chdir=${WORKSPACE}/formacion-jenkins-caso-de-uso-real mvn validate compile test package verify install"
                }
            }
        }
    }
}
