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
                    //Entrando al directorio del repositorio clonado
                    dir("formacion-jenkins-caso-de-uso-real") {
                        //Ejecutando maven con todas las fases deseadas
                        sh "mvn validate compile test package verify install"
                    }
                }
            }
        }
        stage("Docker"){
            steps{
                script{
                    //Oteniendo la ruta  ynombre del JAR
                    def jarName = sh(returnStdout: true, script: "chdir=${WORKSPACE} find -name *.jar*")
                    jarName = jarName.split("/")
                    jarName = jarName[jarName.size() - 1]
                    def jarPath = "${WORKSPACE}/formacion-jenkins-caso-de-uso-real/target/" + jarName
                    
                    //Creando el Dockerfile
                    sh "touch Dockerfile"
                    sh "echo 'FROM java:8\n' >> Dockerfile"
                    sh "echo 'ADD ${jarPath} ${jarName}\n' >> Dockerfile"
                    sh "echo 'CMD java - jar ${jarName}' >> Dockerfile"
                    
                    //Printenado el contenido del Dockerfile
                    def dockerfileContent = sh (returnStdout: true, script: "cat Dockerfile")
                    echo "[DEBUG] Dockerfile content:\n${dockerfileContent}"
                }
            }
        }
    }
}
