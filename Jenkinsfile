pipeline {
    agent none

    stages {

        /* stages for worker app */
        stage('worker-build') {
            when {
                changeset '**/worker/**'
            }

            agent {
                docker {
                    image 'maven:3.9.8-sapmachine-21'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }

            steps {
                echo 'Compiling worker app..'
                dir('worker') {
                    sh 'mvn compile'
                }
            }
        }

        stage('worker-test') {
            when {
                changeset '**/worker/**'
            }
            agent {
                docker {
                    image 'maven:3.9.8-sapmachine-21'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                echo 'Running Unit Tets on worker app..'
                dir('worker') {
                    sh 'mvn clean test'
                }
            }
        }

        stage('worker-package') {
            when {
                branch 'master'
                changeset '**/worker/**'
            }
            agent {
                docker {
                    image 'maven:3.9.8-sapmachine-21'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                echo 'Packaging worker app'
                dir('worker') {
                    sh 'mvn package -DskipTests'
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }

        /* stages for result app */

        stage('result-build') {
            when {
                changeset '**/result/**'
            }
            steps {
                echo 'Compiling result app..'
                dir('result') {
                    sh 'npm install'
                }
            }
        }

        stage('result-test') {
            when {
                changeset '**/result/**'
            }
            steps {
                echo 'Running Unit Tests on result app..'
                dir('result') {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }

        /* stages for vote app */

        stage('vote-build') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    args '--user root'
                }
            }

            steps {
                echo 'Compiling vote app.'
                dir('vote') {
                        sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('vote-test') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    args '--user root'
                }
            }
            steps {
                echo 'Running Unit Tests on vote app.'
                dir('vote') {
                        sh 'pip install -r requirements.txt'
                        sh 'nosetests -v'

                }
            }
        }

    }

    post {
        always {
            echo 'Building multibranch monopipeline is completed..'
        }
    }
}
