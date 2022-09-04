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
                    //Purgando imágenes en desuso
                    sh "docker image prune -a -f"

                    //Oteniendo la ruta  ynombre del JAR
                    def jarName = sh(returnStdout: true, script: "chdir=${WORKSPACE} find -name *.jar*").trim()
                    jarName = jarName.split("/")
                    jarName = jarName[jarName.size() - 1].trim()
                    def jarPath = "formacion-jenkins-caso-de-uso-real/target/" + jarName
                    
                    //Creando el Dockerfile
                    sh "touch Dockerfile"
                    sh "echo -e 'FROM openjdk' >> Dockerfile"
                    sh "echo -e 'COPY ${jarPath} ${jarName}' >> Dockerfile"
                    sh "echo -e 'CMD java -jar ${jarName}' >> Dockerfile"
                    
                    //Printenado el contenido del Dockerfile
                    def dockerfileContent = sh (returnStdout: true, script: "cat Dockerfile")
                    echo "[DEBUG] Dockerfile content:\n${dockerfileContent}"

                    //Creando un nombre para la imagen docker
                    jarName = jarName.split("-")
                    def dockerName = jarName[0].toLowerCase() + ":" + jarName[1].toLowerCase()

                    //Build del Dockerfile 
                    sh "docker image build -f Dockerfile -t ${dockerName} ."
                }
            }
        }
        stage("Helm"){
            steps{
                script{
                    dir("formacion-jenkins-caso-de-uso-real/helm"){

                        //Imprimiendo el resultado de aplicar el values sobre el template
                        sh "helm template -f values.yaml ."
                        
                        //Linter de Helm
                        def out = sh(returnStdout: true, script: "helm lint -f values.yaml .")
                        echo "${out}"
                        if(!out.contains("0 chart(s) failed")){
                            currentBuild.result = "FAILURE"
                            throw new Exception("Linted helm chart failed!!!")
                        }

                        //Desplegando chart
                        withCredentials([file(credentialsId: 'KUBECONFIG', variable: 'KUBECONFIG')]) {
                            sh """
                                set +x
                                helm install formacion-jenkins . --kubeconfig ${KUBECONFIG}
                                set -x
                            """
                        }
                    }
                }
            }
        }
    }
}
