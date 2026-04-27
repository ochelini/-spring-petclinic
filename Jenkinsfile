environment {
    ARTIFACTORY_URL  = 'http://172.17.0.1:8081/artifactory'
    ARTIFACTORY_REPO = 'libs-release-local'
}
pipeline {
    agent any

    environment {
        ARTIFACTORY_URL = 'http://172.17.0.1:8081/artifactory'
        ARTIFACTORY_REPO = 'libs-release-local'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                sh '''
                  trivy fs \
                    --severity HIGH,CRITICAL \
                    --exit-code 0 \
                    
                
            }
        }

       stage('Upload to Artifactory') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'artifactory-creds',
                usernameVariable: 'ARTIFACTORY_USER',
                passwordVariable: 'ARTIFACTORY_PASSWORD'
            )
        ]) {
            sh '''
              set -e
              jfrog --version
              jfrog rt u "target/*.jar" ${ARTIFACTORY_REPO}/ \
                --url=${ARTIFACTORY_URL} \
                --user=$ARTIFACTORY_USER \
                --password=$ARTIFACTORY_PASSWORD \
                --flat=true
            '''
        }
    }
}

        stage('Publish Build Info') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'artifactory-creds',
                        usernameVariable: 'ARTIFACTORY_USER',
                        passwordVariable: 'ARTIFACTORY_PASSWORD'
                    )
                ]) {
                    sh '''
                      jfrog rt build-collect-env
                      jfrog rt build-add-git
                      jfrog rt build-publish petclinic ${BUILD_NUMBER} \
                        --url=$ARTIFACTORY_URL \
                        --user=$ARTIFACTORY_USER \
                        --password=$ARTIFACTORY_PASSWORD
                    
                }
            }
        }
        stage('Docker Build & Push') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'artifactory-creds',
                usernameVariable: 'ART_USER',
                passwordVariable: 'ART_PASS'
            )
        ]) {
            sh '''
              docker build -t petclinic:${BUILD_NUMBER} .
              docker login 172.17.0.1:8082 -u $ART_USER -p $ART_PASS
              docker tag petclinic:${BUILD_NUMBER} 172.17.0.1:8082/petclinic:${BUILD_NUMBER}
              docker push 172.17.0.1:8082/petclinic:${BUILD_NUMBER}
            
        }
    }
}
    }
}
``
