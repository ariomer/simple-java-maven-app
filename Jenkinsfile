pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/ariomer/com.kypnt.rec-tool.rec-svc-maven.git'
            }
        }
        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonarqube-container') {
                    // Optionally use a Maven environment you've configured already
                     withMaven(maven:'maven') {
                        sh 'mvn - f pom.xml clean package -DskipTests sonar:sonar'
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
        //if (sonarResultStatus != 'OK') {  
        //error "Quality gate failure for SonarQube: ${sonarResultStatus}"
        //} 
        //else {
    
                //echo "waitForQualityGate status is ${sonarResultStatus} (tries=${tries})"
                //echo 'Quality Gate: SUCCESS'

                //stage('Build Gradle') {
                //    echo "Build Project ..."
                //    sh "mvn - f pom.xml clean package -DskipTests"
                //    echo "Pull out jar to workdir"
                //    sh "cp /workspace/target/*.jar app.jar"
                //}

                //stage('Build Container Image And Run') {
                //echo "Stopping Service..."
                //// sh "docker stop ${SERVICE_NAME} || true"
                //echo "Removing Container..."
                //// sh "docker rm -f \$(docker ps -aqf \"name=${SERVICE_NAME}\") || true"
                //echo "Removing Image..."
                //// sh "docker rmi \$(docker images | grep '${SERVICE_NAME}') || true"
                //echo "Building Image..."
                //// sh "docker build --file=Dockerfile --tag=${SERVICE_NAME}:latest --rm=true ."
                //echo "Running Image..."
                //// sh "docker run -d --rm --log-driver=gelf --log-opt gelf-address=${LOGSTASH_URI} --network=${SERVICE_TEST_NETWORK} --name=${SERVICE_NAME} --publish=${SERVICE_PORT}:${SERVICE_PORT} --volume=${SERVICE_NAME}-vol:/var/lib/${SERVICE_NAME}/${SERVICE_NAME}-vol ${SERVICE_NAME}:latest"
                //} 
        //}
    }
}
