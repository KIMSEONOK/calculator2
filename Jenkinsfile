pipeline {
    triggers {
        pollSCM('*/5 * * * *')
    }
    agent any
    stages {
        stage ('checkout'){
            steps {
                git url: 'https://github.com/juht/calculator2.git'
            }
        }
        stage ('Compile'){
            steps {
                sh './gradlew compileJava'
            }
        }
        stage ('Unit Test'){
            steps {
                sh "./gradlew test"
            }
        }
        stage("Code coverage") {
            steps {
                sh "./gradlew jacocoTestReport"
                /* publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFile: 'index.html',
                    reportName: "JaCoCo Report"
                ]) */
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
        stage("Static Code Coverage"){
            steps {
                sh "./gradlew checkstyleMain"
                /*publishHTML ( target: [
                    reportDir: 'build/reports/checkstyle/',
                    reportFile: 'main.html',
                    reportName: "CheckStyle Report"
                ]) */
            }
        }
        stage ("Package"){
            steps {
                sh "./gradlew build"
            }
        }
        stage ("Docker build") {
            steps {
                sh "docker build -t localhost:5000/juht/calculator ."
            }
        }
        stage ("Docker push"){
            steps {
                sh "docker push localhost:5000/juht/calculator"
            }
        }
        stage ("Deploy to staging"){
            steps {
                sh "docker-compose up -d"
            }
        }
        stage ("Acceptance Test"){
            steps {
                sleep 60
                sh "./acceptance_test.sh"
            }
        }
    }
    post {
        always {
            sh "docker-compose down"
        }
    }
}
