
pipeline {
    agent any

    environment {
        TOMCAT_HOME = 'D:\\Tomcat 11.0'
        WAR_NAME = 'ci-webapp.war'
    }

    stages {

        stage('1. Verification de l environnement') {
            steps {
                bat '''
                    echo ==== ENVIRONNEMENT JENKINS ====

                    java -version
                    if errorlevel 1 exit /b 1

                    call mvn -version
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('2. Build Maven et tests') {
            steps {
                bat '''
                    echo ==== BUILD MAVEN ET TESTS ====

                    call mvn -B clean verify
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('3. Verification du WAR') {
            steps {
                bat '''
                    echo ==== VERIFICATION DU WAR ====

                    if not exist target\\ci-webapp.war (
                        echo ERREUR : fichier WAR absent
                        dir target
                        exit /b 1
                    )

                    echo FICHIER WAR TROUVE
                    dir target\\ci-webapp.war
                '''
                archiveArtifacts artifacts: 'target/ci-webapp.war',
                                 fingerprint: true
            }
        }

        stage('4. Deploiement sur Tomcat') {
            steps {
                bat '''
                    echo ==== DEPLOIEMENT TOMCAT ====

                    set "WAR_SOURCE=%WORKSPACE%\\target\\ci-webapp.war"

                    if not exist "%WAR_SOURCE%" (
                        echo ERREUR : WAR source absent
                        exit /b 1
                    )

                    rmdir /S /Q "%TOMCAT_HOME%\\webapps\\ci-webapp"
                    del /Q "%TOMCAT_HOME%\\webapps\\ci-webapp.war"

                    copy /Y "%WAR_SOURCE%" "%TOMCAT_HOME%\\webapps\\ci-webapp.war"

                    if errorlevel 1 exit /b 1

                    echo DEPLOIEMENT TERMINE
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD TERMINE AVEC SUCCES'
        }

        failure {
            echo 'ECHEC DU PIPELINE CI/CD'
        }
    }
}
