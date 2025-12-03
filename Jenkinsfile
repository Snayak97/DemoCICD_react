// pipeline {
//     agent any

//     environment {
//         GIT_URL = "https://github.com/Snayak97/DemoCICD_react.git"
//         GIT_BRANCH = "main"
//         DOCKERHUB_REPO = "snayak97/soumya1"
//         APP_NAME = "demo_reactapp"

//         DEV_COMPOSE_FILE = "docker-compose.dev.yml"
//         STAGING_COMPOSE_FILE = "docker-compose.stage.yml"
//         PROD_COMPOSE_FILE = "docker-compose.prod.yml"

//         DEV_SERVER = "ubuntu@ec2-54-83-66-74.compute-1.amazonaws.com"
//         STAGING_SERVER = "ubuntu@ec2-3-95-18-173.compute-1.amazonaws.com"
//         PROD_SERVER = "ubuntu@ec2-44-220-158-39.compute-1.amazonaws.com"

//         // PREVIOUS_VERSION = ""
//         DEPLOYMENT_STATUS = "PENDING"
//         SKIP_DOCKER_BUILD = "false"
//         // VERSION = ""
//     }

//     options {
//         timestamps()
//         buildDiscarder(logRotator(numToKeepStr: '10'))
//         disableConcurrentBuilds()
//         skipDefaultCheckout(true)
//         ansiColor('xterm')
//     }

//     parameters {
//         choice(name: 'VERSION_TYPE', choices: ['PATCH','MINOR','MAJOR'], description: 'Select version increment type')
//         choice(name: 'DEPLOY_TO', choices: ['development', 'staging', 'production', 'all'], description: 'Select deployment target')
//         booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Skip tests')
//         booleanParam(name: 'FORCE_DEPLOY', defaultValue: false, description: 'Force deployment even if quality gate fails')
//     }

//     stages {

//         stage('Fetch Latest Version') {
//             steps {
//                 script {
//                     try {
//                         echo "Fetching Docker tags for ${DOCKERHUB_REPO}..."

//                         def curlStatus = sh(
//                             script: """
//                                 curl -s -f "https://hub.docker.com/v2/repositories/${DOCKERHUB_REPO}/tags/?ordering=last_updated" -o docker_tags.json
//                                 echo \$?
//                             """,
//                             returnStdout: true
//                         ).trim()

//                         if (curlStatus != "0") {
//                             error "Failed to fetch tags from Docker Hub. curl exited with status: ${curlStatus}"
//                         }

//                         def previousVersion = sh(
//                             script: 'jq -r \'.results[] | select(.name|test("^v[0-9]+\\\\.[0-9]+\\\\.[0-9]+$")) | .name\' docker_tags.json | sort | tail -n1',
//                             returnStdout: true
//                         ).trim()

//                         if (!previousVersion) {
//                             previousVersion = "v0.0.0"
//                             echo "No previous version found. Starting from ${previousVersion}"
//                         } else {
//                             echo "Previous version: ${previousVersion}"
//                         }

//                         def version = incrementVersion(previousVersion, params.VERSION_TYPE)
//                         echo "Next version: ${version}"

//                         if (previousVersion == version) {
//                             error "Pipeline aborted: New version (${version}) is same as previous version (${previousVersion})"
//                         }

//                         env.PREVIOUS_VERSION = previousVersion
//                         env.VERSION = version

//                     } catch (Exception e) {
//                         error "Stage 'Fetch Latest Version' failed: ${e.getMessage()}"
//                     }
//                 }
//             }
//         }
//          stage('Cleanup Workspace') {
//             steps {
//                 script {
//                     echo "========== CLEANUP START =========="
//                     try {
//                         deleteDir()
//                         echo "Workspace cleaned"

//                         sh '''
//                           which docker || { echo "Docker not installed"; exit 1; }
//                           docker compose down -v || echo "No running containers"
//                         '''
//                     } catch (err) {
//                         echo "Cleanup failed: ${err}"
//                         error("Stopping pipeline — cleanup stage failed.")
//                     }
//                     echo "========== CLEANUP END =========="
//                 }
//             }
//         }
        
//         stage('Checkout Code') {
//             steps {
//                 script {
//                     echo "========== CHECKOUT START =========="
//                     retry(3) {
//                         try {
//                             checkout([
//                                 $class: 'GitSCM',
//                                 branches: [[name: "*/${GIT_BRANCH}"]],
//                                 userRemoteConfigs: [[url: "${GIT_URL}"]]
//                             ])
//                             echo "Checkout successful"
//                         } catch (err) {
//                             echo "Git checkout failed: ${err}"
//                             echo "Retrying in 3 seconds..."
//                             sleep 3
//                             throw err
//                         }
//                     }
//                     echo "========== CHECKOUT END =========="
//                 }
//             }
//         }
        
//         stage('Debug Workspace') {
//             steps {
//                 script {
//                     echo "========== WORKSPACE DEBUG START =========="
//                     try {
//                         sh '''
//                           echo "---- Current Directory ----"
//                           pwd
//                           echo "---- Files in Workspace ----"
//                           ls -la
//                         '''

//                         def requiredFiles = [
//                             "Dockerfile",
//                             env.DEV_COMPOSE_FILE,
//                             env.STAGING_COMPOSE_FILE,
//                             env.PROD_COMPOSE_FILE,
//                             "Jenkinsfile"
//                         ]

//                         def missingFiles = []
//                         for (file in requiredFiles) {
//                             if (!fileExists(file)) {
//                                 missingFiles << file
//                             } else {
//                                 echo "Found required file: ${file}"
//                             }
//                         }

//                         if (missingFiles.size() > 0) {
//                             error("Pipeline aborted: Missing required file(s): ${missingFiles.join(', ')}")
//                         }

//                         echo "Workspace validation passed: All required files are present"
//                     } catch (err) {
//                         echo "Workspace validation failed: ${err}"
//                         error("Stopping pipeline — required files missing or workspace check failed.")
//                     }
//                     echo "========== WORKSPACE DEBUG END =========="
//                 }
//             }
//         }
        
//         stage('Install Dependencies') {
//             steps {
//                 script {
//                     echo "========== INSTALL DEPENDENCIES START =========="
//                     try {
//                         sh '''
//                           which node || { echo "Node.js not installed"; exit 1; }
//                           node -v
//                           npm -v
//                         '''
//                         sh '''
//                           npm ci --prefer-offline
//                         '''
//                         echo "Dependencies installed successfully (using npm ci --prefer-offline)"
//                     } catch (err) {
//                         echo "Dependency installation failed: ${err}"
//                         error("Stopping pipeline — Install Dependencies failed.")
//                     }
//                     echo "========== INSTALL DEPENDENCIES END =========="
//                 }
//             }
//         }
        
//         stage("unit test"){
//             steps{
//                 echo "Version: ${env.VERSION}"
//                 echo " P_ ver: ${env.PREVIOUS_VERSION}"
//             }
//         }
//         stage('Build React App') {
//             steps {
//                 script {
//                     echo "========== BUILD START =========="
//                     try {
//                         sh '''
//                           echo "Building React app..."
//                           npm run build
//                         '''
//                         echo "React app built successfully"
//                     } catch (err) {
//                         echo "Build failed: ${err}"
//                         error("Stopping pipeline — Build React App failed.")
//                     }
//                     echo "========== BUILD END =========="
//                 }
//             }
//         }
        
//         stage('Docker Login') {
//             steps {
//                 script {
//                     echo "========== DOCKER LOGIN START =========="
//                     try {
//                         withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
//                                                          usernameVariable: 'DOCKERHUB_USER',
//                                                          passwordVariable: 'DOCKERHUB_PASS')]) {
//                             sh '''
//                                 which docker >/dev/null 2>&1 || { echo "Docker not installed"; exit 1; }
//                                 echo "Logging into Docker Hub..."
//                                 echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
//                                 docker info | grep Username
//                             '''
//                         }
//                         echo "Docker login successful."
//                     } catch (err) {
//                         echo "Docker login failed: ${err}"
//                         error("Stopping pipeline — Docker Login stage failed.")
//                     }
//                     echo "========== DOCKER LOGIN END =========="
//                 }
//             }
//         }
        
//         stage('Check Docker Image Existence') {
//             steps {
//         script {
//             echo "========== CHECK DOCKER IMAGE START =========="

//             try {

//                 if (!env.VERSION) {
//                     error("VERSION is not set. Cannot check Docker image existence.")
//                 }

//                 withCredentials([
//                     usernamePassword(
//                         credentialsId: 'dockerhub-creds',
//                         usernameVariable: 'DOCKERHUB_USER',
//                         passwordVariable: 'DOCKERHUB_PASS'
//                     )
//                 ]) {

                    
//                     def token = sh(
//                         script: """
//                             curl -s -X POST \
//                                 -H "Content-Type: application/json" \
//                                 -d '{ "username": "$DOCKERHUB_USER", "password": "$DOCKERHUB_PASS" }' \
//                                 https://hub.docker.com/v2/users/login/ \
//                                 | jq -r '.token'
//                         """,
//                         returnStdout: true
//                     ).trim()

//                     if (!token || token == "null") {
//                         echo "Authentication failed while fetching token."
//                         env.SKIP_DOCKER_BUILD = "false"
//                         throw new Exception("DockerHub Token Fetch Failed")
//                     }

                   
//                     def httpStatus = sh(
//                         script: """
//                             curl -s -o /dev/null -w "%{http_code}" \
//                             -H "Authorization: JWT ${token}" \
//                             https://hub.docker.com/v2/repositories/${env.DOCKERHUB_REPO}/tags/${env.VERSION}/
//                         """,
//                         returnStdout: true
//                     ).trim()

//                     if (httpStatus == "200") {
//                         echo " Image already exists: ${env.DOCKERHUB_REPO}:${env.VERSION}"
//                         env.SKIP_DOCKER_BUILD = "true"
//                     } else {
//                         echo "Image not found — will build new image."
//                         env.SKIP_DOCKER_BUILD = "false"
//                     }
//                 }

//             } catch (err) {
//                 echo "Error checking Docker image: ${err}"
//                 echo "Proceeding with image build."
//                 env.SKIP_DOCKER_BUILD = "false"
//             }

//             echo "========== CHECK DOCKER IMAGE END =========="
//         }
//     }
//         }
        
//         stage('Docker Build') {
//             when {
//         expression { env.SKIP_DOCKER_BUILD == "false" }
//     }
//             steps {
//         script {
//             echo "========== DOCKER BUILD START =========="

//             try {

//                 if (!env.VERSION) {
//                     error("VERSION is not set — cannot build Docker image.")
//                 }

//                 def DOCKERHUB_IMAGE = "${DOCKERHUB_REPO}:${VERSION}"

//                 sh """
//                     set -e

//                     echo "Building Docker image..."
//                     docker build -t ${DOCKERHUB_IMAGE} .

//                     echo "Docker build completed."
//                 """

//                 echo "Image created: ${DOCKERHUB_IMAGE}"

//             } catch (err) {
//                 echo "Docker Build Failed: ${err}"
//                 error("Stopping pipeline — Docker Build stage failed.")
//             }

//             echo "========== DOCKER BUILD END =========="
//         }
//     }
//         }
        
//         stage('Docker Push') {
//          when {
//         expression { env.SKIP_DOCKER_BUILD == "false" }
//     }
//          steps {
//         script {
//             echo "========== DOCKER PUSH START =========="

//             try {
//                 if (!env.VERSION) {
//                     error("VERSION is not set — cannot push Docker image.")
//                 }

//                 def FULL_IMAGE = "${DOCKERHUB_REPO}:${VERSION}"

//                 echo "Preparing to push image: ${FULL_IMAGE}"

//                 sh """
//                     set -e
//                     echo "Pushing Docker image to DockerHub..."
//                     docker push ${FULL_IMAGE}
//                 """

//                 echo "Docker Push successful: ${FULL_IMAGE}"

//             } catch (err) {
//                 echo "Docker Push FAILED: ${err}"
//                 error("Stopping pipeline — Docker Push stage failed.")
//             }

//             echo "========== DOCKER PUSH END =========="
//         }
//     }
//         }




//     } // stages

//     post {
//         always {
//             echo "Cleaning up post-build..."
//             sh "docker logout || true"
//         }
//         success {
//             echo "Pipeline completed successfully!"
//         }
//         failure {
//             echo "Pipeline failed. Check logs for details."
//         }
//     }

// } 

// // Define function at the bottom outside the pipeline
// def incrementVersion(String currentVersion, String type) {
//     def parts = currentVersion.replace("v", "").split("\\.")
//     def major = parts[0].toInteger()
//     def minor = parts[1].toInteger()
//     def patch = parts[2].toInteger()

//     if (type == 'PATCH') {
//         patch++
//     } else if (type == 'MINOR') {
//         minor++
//         patch = 0
//     } else if (type == 'MAJOR') {
//         major++
//         minor = 0
//         patch = 0
//     }

//     return "v${major}.${minor}.${patch}"
// }
// def PREVIOUS_VERSION = ""
// def VERSION = ""
