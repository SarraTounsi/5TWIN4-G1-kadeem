pipeline {
    agent any

    stages {
        stage('Git') {
            steps {

                    git branch: 'tounsisarra_5twin4_G1',
                        url: 'https://github.com/SarraTounsi/5TWIN4-G1-kadeem'

            }
        }

        stage('mvn_clean') {
            steps {
                script {
                    sh 'mvn clean'       }
            }
        }
          stage('mvn_compile') {
            steps {
                script {
                   sh 'mvn compile'      }
            }
        }
            stage('MVN SONARQUBE') {
                steps {
                    script {
                        sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=sonar -Dmaven.test.skip=true';
                   }
                }
        }

            stage('Unit Tests') {
                            steps {
                                sh 'mvn clean test -Pmockito-tests'
                            }
                     post {
                          always {
                                    junit(
                                        allowEmptyResults: true,
                                        testResults: 'target/surefire-reports/**/*.xml'
                                    )
                                }
                            }
           }

            stage('Deploy TO NEXUS') {
                            steps {
                                sh 'mvn deploy -Dmaven.test.skip=true'
                            }
                        }
         stage('Docker Image') {
            steps {
                 sh 'docker build -t sarratounsi-5twin4-g1 .'
                    }
                }

  stage('Push Docker Image') {
            steps {
                  sh "docker login -u sarratounsi -p sarra123!"
                 sh "docker tag sarratounsi-5twin4-g1 sarratounsi/sarratounsi-5twin4-g1:v2"
                 sh "docker push  sarratounsi/sarratounsi-5twin4-g1:v2"
            }
        }
            stage('Docker Compose') {
                  steps {
                    sh 'docker compose up -d'
                             }
                   }
            stage('Run grafana & prometheus') {
                               steps {
                                   sh 'docker start prometheus'
                                   sh 'docker start grafana'
                                   echo 'Running grafana on http://192.168.33.10:3000/'
                               }
                           }



    }
}
 post {
        success {
            echo 'Build successful'
        }
        failure {
            echo 'Build failed'
        }
    }
}