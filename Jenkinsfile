// curl -X POST -F "jenkinsfile=<Jenkinsfile" http://ccbvtauto.eur.ad.sag:8080/pipeline-model-converter/validate

pipeline {
    agent none
    parameters {
        choice(choices: '10.3\n10.2\n10.1', description: 'Test templates for this release', name: 'release')
        choice(choices: '[]\nALL', description: 'Fixes to install', name: 'fixes')
    }
    stages {
        stage("Test") {
            environment {
                TAG = "${params.release}"
            }
            parallel {
                stage('Runtimes') {
                    agent { label 'docker' }
                    environment {
                        COMPOSE_PROJECT_NAME = 'sagdevops-templates'
                    }
                    steps {
                        sh 'docker-compose pull cc'
                        sh 'docker-compose up -d cc'

                        sh "./provisionw sag-um-server um.fixes=${params.fixes}"
                        sh "./provisionw sag-um-config um.url=nsp://node:9000"
                        sh "./provisionw sag-tc-server tc.fixes=${params.fixes}"
                        sh "./provisionw sag-is-server is.fixes=${params.fixes}"
                        sh "./provisionw sag-is-config is.um.url=nsp://node:9000"
                        sh "./provisionw sag-des des.fixes=${params.fixes} des.um.url=nsp://node:9000"
                    }
                    post {
                        always {
                            sh 'docker-compose down'
                        }
                    }    
                }
                stage('Designer and tools') {
                    agent { label 'docker' }
                    environment {
                        COMPOSE_PROJECT_NAME = 'sagdevops-templates'
                    }
                    steps {
                        sh 'docker-compose pull cc'
                        sh 'docker-compose up -d cc'

                        sh "./provisionw sag-abe abe.fixes=${params.fixes}"                        
                        sh "./provisionw sag-designer-services designer.fixes=${params.fixes}"
                    }
                    post {
                        always {
                            sh 'docker-compose down'
                        }
                    }    
                }
            }
        }
    }
}
