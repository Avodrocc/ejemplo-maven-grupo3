import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Paso 1: Compliar"){
            steps {
                script {
                sh "echo 'Compile Code!'"
                // Run Maven on a Unix agent.
                sh "mvn clean compile -e"
                }
            }
        }
        stage("Paso 2: Testear"){
            steps {
                script {
                sh "echo 'Test Code!'"
                // Run Maven on a Unix agent.
                sh "mvn clean test -e"
                }
            }
        }    
        stage("Paso 3: Build .Jar"){
            steps {
                script {
                sh "echo 'Build .Jar!'"
                // Run Maven on a Unix agent.
                sh "mvn clean package -e"
                }
            }  
            post {
                //record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts:'build/*.jar'
                }
            }
        }  
        stage("Paso 4: Análisis SonarQube"){
            steps {
                withSonarQubeEnv('sonar') {
                    sh "echo 'Calling sonar Service in another docker container!'"
                    // Run Maven on a Unix agent to execute Sonar.
                    sh 'mvn clean verify sonar:sonar'
                }
            }  
        }
	        stage("Paso 5: Levantar Springboot APP"){
            steps {
                sh 'mvn spring-boot:run &'
            }
        }
        stage("Paso 6: Dormir(Esperar 10sg) "){
            steps {
                sh 'sleep 30'
            }
        }
        stage("Paso 7: Test Alive Service - Testing Application!"){
            steps {
                sh 'curl -X GET "http://localhost:8081/rest/mscovid/test?msg=testing"'
            }
        }
        stage('Paso 8: Subir a Nexus') {
            steps {
                nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'devops-usach-nexus', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'build/DevOpsUsach2020-0.0.1.jar']], mavenCoordinate: [artifactId: 'DevOpsUsach2020', groupId: 'com.devopsusach2020', packaging: 'jar', version: '1.0.0']]]
            }
        }


    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'fase success'"
        }
        failure {
            sh "echo 'fase failure'"
        }
    }
}


