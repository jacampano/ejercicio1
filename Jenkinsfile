//Configuración repositorio 
def credencialesGIT = "Gitlab"
def urlGIT = "http://gitlab.example.com/gitlab-instance-21f8eb48/clase/pruebaswebapp.git"
def rama = "master"

// Herramientas y servidores
def versionJDK = "JDK11"
def versionMaven = "Maven-3.8.6"


def realizarCompilacion = true
def realizarDespliegue = true

pipeline {
    
    agent any
          
    stages {
        // Preparación tareas previas. Ejecución de tareas previas preparatorias de la ejecución de la Tubería.        
        stage('Inicialización')
        {
            steps{
               
                cleanWs()
      
            }
        }
        //Descarga código fuente
        stage('Descarga código fuente') {
            steps {
                
                echo "--- Obtener Codigo Fuente desde rama:" + rama + "--"
                checkout([$class: 'GitSCM',
                       branches: [[name: "${rama}"]],
                       doGenerateSubmoduleConfigurations: false,
                       extensions: [], gitTool: 'git', submoduleCfg: [],
                       userRemoteConfigs: [[credentialsId: "${credencialesGIT}", url: "${urlGIT}"]]])
            }
                
  
        }
        // Compilación y empaquetado
        stage('Compilación y empaquetado') {
              when {
                expression {
                    realizarCompilacion == true
                }
            }
          
                steps {
                    script {
                        
                        withEnv(["JAVA_HOME=${ tool versionJDK}"]) {
                            withMaven(maven: versionMaven) {

                            echo "--- Compilación y empaquetado ---"
                            echo "JAVA_HOME: $JAVA_HOME"
                            sh "mvn clean package"
                                       
                            }
                        }
         
                    }
                 
                }
            }
        

        //Aqui nueva fase que realiza el despliegue de un WAR.
        stage('Deploy') {
                when {
                    expression {
                        realizarDespliegue == true
                    }
                }

             steps {
                    script {
                        deploy adapters: [tomcat9(credentialsId: "tomcat", path: '', url: "http://localhost:9999/")], contextPath: "/pruebasWebApp", war: "**/*.war"
                    }
                }

        }

    }
        
        post {
            success {
                echo "Acción cuando se completa con éxito"
            }
            aborted {
                
               echo "--- Aciones cuando se aborta ---"
                
            }
            failure {
                
                echo "-- Aciones cuando se falla ---"
              
                
            }

            always {
                echo "--- SE EJECUTA SIEMPRE ---"
            }
        } 
    }


