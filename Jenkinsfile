pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/ariomer/simple-java-maven-app.gite'
            }
        }
        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonarqube-container') {
                    // Optionally use a Maven environment you've configured already
                     withMaven(maven:'maven') {
                        sh 'mvn -B -DskipTests clean package sonar:sonar'
                    }
                }
            }
        }
        stage('Quality Gate') {
            withSonarQubeEnv() {
                while ((sonarResultStatus == "PENDING" || sonarResultStatus == "IN_PROGRESS") && tries++ < 5) {
                try {
                    sonarResult = waitForQualityGate abortPipeline: true
                    sonarResultStatus = sonarResult.status
                } catch(ex) {
                    echo "caught exception ${ex}"
                } finally {
                    sonarResultStatus = 'OK'
                }
                }
            }
        }
    }
}
