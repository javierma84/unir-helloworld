pipeline {
    agent any

     stages {
        stage('Get Code') {
            steps {
                // Obtener código del repo
                //git 'https://github.com/anieto-unir/helloworld.git'
		script {
			scmVars = checkout scm
			echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
		}
            }
        }
        
        stage('Build') {
            steps {
                echo 'Eyyy, esto es Python. No hay que compilar nada!!!'
		echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\''
            }
        }
        
        stage('Tests') {
            parallel {
                stage('Unit') {
                    steps {
                        sh '''
                            export PYTHONPATH=$WORKSPACE
                            pytest --junitxml=result-unit.xml test/unit
                        '''
                    }
                }
                stage('Service') {
                    steps{
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            sh '''
                                export FLASK_APP='app/api.py'
                                echo $FLASK_APP
                                export FLASK_ENV='development'
                                echo $FLASK_ENV
                                flask run &
                                java -jar /usr/share/wiremock/wiremock-jre8-standalone-2.33.2.jar --port 9090 --root-dir /usr/share/wiremock &
                                sleep 1s
                                pytest --junitxml=result-rest.xml test/rest
                            '''
                        }
                    }
                }
            }
        }
        stage ('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}
