pipeline {
    agent any

    environment {
        GIT_URL = "https://github.com/Snayak97/DemoCICD_react.git"
        GIT_BRANCH = "main"
        DOCKERHUB_REPO = "snayak97/soumya1"
        APP_NAME = "demo_reactapp"

        DEV_COMPOSE_FILE = "docker-compose.dev.yml"
        STAGING_COMPOSE_FILE = "docker-compose.stage.yml"
        PROD_COMPOSE_FILE = "docker-compose.prod.yml"

        DEV_SERVER = "ubuntu@ec2-50-16-67-80.compute-1.amazonaws.com"
        STAGING_SERVER = "ubuntu@ec2-3-95-18-173.compute-1.amazonaws.com"
        PROD_SERVER = "ubuntu@ec2-44-220-158-39.compute-1.amazonaws.com"

        // PREVIOUS_VERSION = ""
        DEPLOYMENT_STATUS = "PENDING"
        SKIP_DOCKER_BUILD = "false"
        // VERSION = ""
        
        // url
        DEV_URL = "http://54.85.128.144:5174/"
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        disableConcurrentBuilds()
        skipDefaultCheckout(true)
        ansiColor('xterm')
    }

    parameters {
        choice(name: 'VERSION_TYPE', choices: ['PATCH','MINOR','MAJOR'], description: 'Select version increment type')
        choice(name: 'DEPLOY_TO', choices: ['development', 'staging', 'production', 'all'], description: 'Select deployment target')
        booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Skip tests')
        booleanParam(name: 'FORCE_DEPLOY', defaultValue: false, description: 'Force deployment even if quality gate fails')
    }

    stages {

        // stage('Fetch Latest Version') {
        //     steps {
        //         script {
        //             try {
        //                 echo "Fetching Docker tags for ${DOCKERHUB_REPO}..."

        //                 def curlStatus = sh(
        //                     script: """
        //                         curl -s -f "https://hub.docker.com/v2/repositories/${DOCKERHUB_REPO}/tags/?ordering=last_updated" -o docker_tags.json
        //                         echo \$?
        //                     """,
        //                     returnStdout: true
        //                 ).trim()

        //                 if (curlStatus != "0") {
        //                     error "Failed to fetch tags from Docker Hub. curl exited with status: ${curlStatus}"
        //                 }

        //                 def previousVersion = sh(
        //                     script: 'jq -r \'.results[] | select(.name|test("^v[0-9]+\\\\.[0-9]+\\\\.[0-9]+$")) | .name\' docker_tags.json | sort | tail -n1',
        //                     returnStdout: true
        //                 ).trim()

        //                 if (!previousVersion) {
        //                     previousVersion = "v0.0.0"
        //                     echo "No previous version found. Starting from ${previousVersion}"
        //                 } else {
        //                     echo "Previous version: ${previousVersion}"
        //                 }

        //                 def version = incrementVersion(previousVersion, params.VERSION_TYPE)
        //                 echo "Next version: ${version}"

        //                 if (previousVersion == version) {
        //                     error "Pipeline aborted: New version (${version}) is same as previous version (${previousVersion})"
        //                 }

        //                 env.PREVIOUS_VERSION = previousVersion
        //                 env.VERSION = version

        //             } catch (Exception e) {
        //                 error "Stage 'Fetch Latest Version' failed: ${e.getMessage()}"
        //             }
        //         }
        //     }
        // }
        //  stage('Cleanup Workspace') {
        //     steps {
        //         script {
        //             echo "========== CLEANUP START =========="
        //             try {
        //                 deleteDir()
        //                 echo "Workspace cleaned"

        //                 sh '''
        //                   which docker || { echo "Docker not installed"; exit 1; }
        //                   docker compose down -v || echo "No running containers"
        //                 '''
        //             } catch (err) {
        //                 echo "Cleanup failed: ${err}"
        //                 error("Stopping pipeline — cleanup stage failed.")
        //             }
        //             echo "========== CLEANUP END =========="
        //         }
        //     }
        // }
        
        // stage('Checkout Code') {
        //     steps {
        //         script {
        //             echo "========== CHECKOUT START =========="
        //             retry(3) {
        //                 try {
        //                     checkout([
        //                         $class: 'GitSCM',
        //                         branches: [[name: "*/${GIT_BRANCH}"]],
        //                         userRemoteConfigs: [[url: "${GIT_URL}"]]
        //                     ])
        //                     echo "Checkout successful"
        //                 } catch (err) {
        //                     echo "Git checkout failed: ${err}"
        //                     echo "Retrying in 3 seconds..."
        //                     sleep 3
        //                     throw err
        //                 }
        //             }
        //             echo "========== CHECKOUT END =========="
        //         }
        //     }
        // }
        
        // stage('Debug Workspace') {
        //     steps {
        //         script {
        //             echo "========== WORKSPACE DEBUG START =========="
        //             try {
        //                 sh '''
        //                   echo "---- Current Directory ----"
        //                   pwd
        //                   echo "---- Files in Workspace ----"
        //                   ls -la
        //                 '''

        //                 def requiredFiles = [
        //                     "Dockerfile",
        //                     env.DEV_COMPOSE_FILE,
        //                     env.STAGING_COMPOSE_FILE,
        //                     env.PROD_COMPOSE_FILE,
        //                     "Jenkinsfile"
        //                 ]

        //                 def missingFiles = []
        //                 for (file in requiredFiles) {
        //                     if (!fileExists(file)) {
        //                         missingFiles << file
        //                     } else {
        //                         echo "Found required file: ${file}"
        //                     }
        //                 }

        //                 if (missingFiles.size() > 0) {
        //                     error("Pipeline aborted: Missing required file(s): ${missingFiles.join(', ')}")
        //                 }

        //                 echo "Workspace validation passed: All required files are present"
        //             } catch (err) {
        //                 echo "Workspace validation failed: ${err}"
        //                 error("Stopping pipeline — required files missing or workspace check failed.")
        //             }
        //             echo "========== WORKSPACE DEBUG END =========="
        //         }
        //     }
        // }
        
        // stage('Install Dependencies') {
        //     steps {
        //         script {
        //             echo "========== INSTALL DEPENDENCIES START =========="
        //             try {
        //                 sh '''
        //                   which node || { echo "Node.js not installed"; exit 1; }
        //                   node -v
        //                   npm -v
        //                 '''
        //                 sh '''
        //                   npm ci --prefer-offline
        //                 '''
        //                 echo "Dependencies installed successfully (using npm ci --prefer-offline)"
        //             } catch (err) {
        //                 echo "Dependency installation failed: ${err}"
        //                 error("Stopping pipeline — Install Dependencies failed.")
        //             }
        //             echo "========== INSTALL DEPENDENCIES END =========="
        //         }
        //     }
        // }
        
        // stage("unit test"){
        //     steps{
        //         echo "Version: ${env.VERSION}"
        //         echo " P_ ver: ${env.PREVIOUS_VERSION}"
        //         echo " P_ ver: ${env.APP_NAME}"
        //     }
        // }
        // stage('Build React App') {
        //     steps {
        //         script {
        //             echo "========== BUILD START =========="
        //             try {
        //                 sh '''
        //                   echo "Building React app..."
        //                   npm run build
        //                 '''
        //                 echo "React app built successfully"
        //             } catch (err) {
        //                 echo "Build failed: ${err}"
        //                 error("Stopping pipeline — Build React App failed.")
        //             }
        //             echo "========== BUILD END =========="
        //         }
        //     }
        // }
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         echo "Starting SonarQube Analysis..."
        //         script {
        //             retry(2) {
        //                 withSonarQubeEnv("sonar") {
        //                     try {
        //                         sh """
        //                             ${SONAR_HOME}/bin/sonar-scanner \
        //                             -Dsonar.projectKey=${APP_NAME} \
        //                             -Dsonar.projectName=${APP_NAME} \
        //                             -Dsonar.sources=. 
        //                         """
        //                         echo "SonarQube analysis completed successfully."
        //                     } catch (Exception err) {
        //                         echo "SonarQube analysis failed: ${err}"
        //                         error "SonarQube stage failed"
        //                     }
        //                 }
        //             }
        //         }
        //     }
        // }
        // stage('Quality Gate Check') {
        //     steps {
        //         timeout(time: 3, unit: 'MINUTES') {
        //             script {
        //                 try {
        //                     def qg = waitForQualityGate(abortPipeline: false)

        //                     echo "Quality Gate Status: ${qg.status}"

        //                     if (qg.status != 'OK') {
        //                         echo "Quality Gate FAILED — Status: ${qg.status}"
        //                         error "Pipeline stopped because Quality Gate failed: ${qg.status}"
        //                     }

        //                     echo "Quality Gate PASSED successfully."

        //                 } catch (Exception err) {
        //                     echo "Error while checking Quality Gate: ${err}"
        //                     error "Quality Gate Check failed unexpectedly."
        //                 }
        //             }
        //         }
        //     }
        // }
        
        // stage('Docker Login') {
        //     steps {
        //         script {
        //             echo "========== DOCKER LOGIN START =========="
        //             try {
        //                 withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
        //                                                  usernameVariable: 'DOCKERHUB_USER',
        //                                                  passwordVariable: 'DOCKERHUB_PASS')]) {
        //                     sh '''
        //                         which docker >/dev/null 2>&1 || { echo "Docker not installed"; exit 1; }
        //                         echo "Logging into Docker Hub..."
        //                         echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
        //                         docker info | grep Username
        //                     '''
        //                 }
        //                 echo "Docker login successful."
        //             } catch (err) {
        //                 echo "Docker login failed: ${err}"
        //                 error("Stopping pipeline — Docker Login stage failed.")
        //             }
        //             echo "========== DOCKER LOGIN END =========="
        //         }
        //     }
        // }
        
    //     stage('Check Docker Image Existence') {
    //         steps {
    //     script {
    //         echo "========== CHECK DOCKER IMAGE START =========="

    //         try {

    //             if (!env.VERSION) {
    //                 error("VERSION is not set. Cannot check Docker image existence.")
    //             }

    //             withCredentials([
    //                 usernamePassword(
    //                     credentialsId: 'dockerhub-creds',
    //                     usernameVariable: 'DOCKERHUB_USER',
    //                     passwordVariable: 'DOCKERHUB_PASS'
    //                 )
    //             ]) {

                    
    //                 def token = sh(
    //                     script: """
    //                         curl -s -X POST \
    //                             -H "Content-Type: application/json" \
    //                             -d '{ "username": "$DOCKERHUB_USER", "password": "$DOCKERHUB_PASS" }' \
    //                             https://hub.docker.com/v2/users/login/ \
    //                             | jq -r '.token'
    //                     """,
    //                     returnStdout: true
    //                 ).trim()

    //                 if (!token || token == "null") {
    //                     echo "Authentication failed while fetching token."
    //                     env.SKIP_DOCKER_BUILD = "false"
    //                     throw new Exception("DockerHub Token Fetch Failed")
    //                 }

                   
    //                 def httpStatus = sh(
    //                     script: """
    //                         curl -s -o /dev/null -w "%{http_code}" \
    //                         -H "Authorization: JWT ${token}" \
    //                         https://hub.docker.com/v2/repositories/${env.DOCKERHUB_REPO}/tags/${env.VERSION}/
    //                     """,
    //                     returnStdout: true
    //                 ).trim()

    //                 if (httpStatus == "200") {
    //                     echo " Image already exists: ${env.DOCKERHUB_REPO}:${env.VERSION}"
    //                     env.SKIP_DOCKER_BUILD = "true"
    //                 } else {
    //                     echo "Image not found — will build new image."
    //                     env.SKIP_DOCKER_BUILD = "false"
    //                 }
    //             }

    //         } catch (err) {
    //             echo "Error checking Docker image: ${err}"
    //             echo "Proceeding with image build."
    //             env.SKIP_DOCKER_BUILD = "false"
    //         }

    //         echo "========== CHECK DOCKER IMAGE END =========="
    //     }
    // }
    //     }
        
    //     stage('Docker Build') {
    //         when {
    //     expression { env.SKIP_DOCKER_BUILD == "false" }
    // }
    //         steps {
    //     script {
    //         echo "========== DOCKER BUILD START =========="

    //         try {

    //             if (!env.VERSION) {
    //                 error("VERSION is not set — cannot build Docker image.")
    //             }

    //             def DOCKERHUB_IMAGE = "${DOCKERHUB_REPO}:${VERSION}"

    //             sh """
    //                 set -e

    //                 echo "Building Docker image..."
    //                 docker build -t ${DOCKERHUB_IMAGE} .

    //                 echo "Docker build completed."
    //             """

    //             echo "Image created: ${DOCKERHUB_IMAGE}"

    //         } catch (err) {
    //             echo "Docker Build Failed: ${err}"
    //             error("Stopping pipeline — Docker Build stage failed.")
    //         }

    //         echo "========== DOCKER BUILD END =========="
    //     }
    // }
    //     }
        
    //     stage('Docker Push') {
    //      when {
    //     expression { env.SKIP_DOCKER_BUILD == "false" }
    // }
    //      steps {
    //     script {
    //         echo "========== DOCKER PUSH START =========="

    //         try {
    //             if (!env.VERSION) {
    //                 error("VERSION is not set — cannot push Docker image.")
    //             }

    //             def FULL_IMAGE = "${DOCKERHUB_REPO}:${VERSION}"

    //             echo "Preparing to push image: ${FULL_IMAGE}"

    //             sh """
    //                 set -e
    //                 echo "Pushing Docker image to DockerHub..."
    //                 docker push ${FULL_IMAGE}
    //             """

    //             echo "Docker Push successful: ${FULL_IMAGE}"

    //         } catch (err) {
    //             echo "Docker Push FAILED: ${err}"
    //             error("Stopping pipeline — Docker Push stage failed.")
    //         }

    //         echo "========== DOCKER PUSH END =========="
    //     }
    // }
    //     }
        
    //     stage('Deploy to EC2 Development') {
    //         when {
    //     anyOf {
    //         expression { params.DEPLOY_TO == 'development' }
    //         expression { params.DEPLOY_TO == 'all' }
    //     }
    // }
    //         steps {
    //             script {
    //         echo "========== DEPLOY TO EC2 DEV START =========="
    //         if (!fileExists(env.DEV_COMPOSE_FILE)) {
    //             error("File not found: ${env.DEV_COMPOSE_FILE}")
    //         }
    //         echo "Found: ${env.DEV_COMPOSE_FILE}"
            
    //         if (!fileExists(".env.dev")) {
    //             error("File not found: .env.dev")
    //         }
    //         echo "Found: .env.dev"
            
    //         try {
    //             deployToEC2(
    //                 env.DEV_SERVER,           // EC2 server
    //                 env.DEV_COMPOSE_FILE,     // docker-compose file
    //                 ".env.dev",               // environment file
    //                 env.DOCKERHUB_REPO,       // Docker repo
    //                 "v1.0.4",              // dynamic version
    //                 env.APP_NAME
    //             )
    //         } catch (err) {
    //             echo "ERROR: Deployment to EC2 failed: ${err}"
    //             error("Pipeline stopped — EC2 deployment failed.")
    //         }
    //         echo "========== DEPLOY TO EC2 DEV END =========="
    //     }
    //         }
    //     }
        
        // stage("Smoke Testing Development"){
        //     steps{
        //         script {
        //             def devUrl = env.DEV_URL
        //             runHttpTest(devUrl, "DEV Smoke Test")
        //         }
                
        //     }
        // }
        
    //     stage('Sanity Test Development') {
    //         steps {
    //     script {
    //         runSanityTest(env.DEV_URL, ["<div id=\"root\">", "<title>"])
    //     }
    // }
    //     }
    
           stage('Approval for Staging Deployment') {
    steps {
        script {
            def confirm = input(
                id: 'Proceed_Staging',
                message: 'Do you want to deploy to STAGING?',
                ok: 'Deploy',
                parameters: [
                    choice(name: 'CONFIRM', choices: ['NO', 'YES'], description: 'Select YES to continue')
                ]
            )

            if(confirm != 'YES') {
                echo "❌ Staging deployment aborted by user."
                env.RUN_STAGING = "false"
            } else {
                echo "✅ User approved STAGING deployment."
                env.RUN_STAGING = "true"
            }
        }
    }
}


           stage('Deploy to Staging') {
                when {
        expression { env.RUN_STAGING == "true" }
    }
                steps {
                script {
            echo "Deploying to STAGING..."
           
        }
                }
            }




     




    } // stages

    post {
        always {
            echo "Cleaning up post-build..."
            sh "docker logout || true"
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }

} 

// Define function at the bottom outside the pipeline
def incrementVersion(String currentVersion, String type) {
    def parts = currentVersion.replace("v", "").split("\\.")
    def major = parts[0].toInteger()
    def minor = parts[1].toInteger()
    def patch = parts[2].toInteger()

    if (type == 'PATCH') {
        patch++
    } else if (type == 'MINOR') {
        minor++
        patch = 0
    } else if (type == 'MAJOR') {
        major++
        minor = 0
        patch = 0
    }

    return "v${major}.${minor}.${patch}"
}
def PREVIOUS_VERSION = ""
def VERSION = ""

def deployToEC2(server, composeFile, envFile, dockerRepo, version, appName) {

    echo """ deploy to develop
    Server: ${server}
    App: ${appName}
    Image: ${dockerRepo}:${version}
    Compose: ${composeFile}
    Env: ${envFile}
    """
    def reachable = sh(
        script: "ssh -o BatchMode=yes -o ConnectTimeout=5 -o StrictHostKeyChecking=no ${server} 'echo 1'",
        returnStatus: true
    )

    if (reachable != 0) {
        error("Cannot connect to server: ${server}. Deployment aborted.")
    }

    sshagent(['jenkins-ssh-cred-id']) {

        // Create app folder
        sh """
            echo "📁 Creating directory on EC2..."
            ssh -o StrictHostKeyChecking=no ${server} \
            "mkdir -p /home/ubuntu/deploy/${appName}"
        """

        // Copy files - keep the original env file name
        sh """
            echo "📤 Copying docker-compose and env files..."
            scp -o StrictHostKeyChecking=no ${composeFile} \
                ${server}:/home/ubuntu/deploy/${appName}/docker-compose.yml
            
            scp -o StrictHostKeyChecking=no ${envFile} \
                ${server}:/home/ubuntu/deploy/${appName}/${envFile}
        """

        // Pull & restart container with VERSION passed
        sh """
            echo " Pulling Docker image and restarting..."
            ssh -o StrictHostKeyChecking=no ${server} "
                cd /home/ubuntu/deploy/${appName}
                
                # Export VERSION for docker-compose
                export VERSION=${version}
                
                echo 'Pulling image: ${dockerRepo}:${version}'
                docker compose pull
                
                echo 'Stopping old containers...'
                docker compose down
                
                echo 'Starting new containers...'
                docker compose up -d --force-recreate
                
                echo 'Container status:'
                docker compose ps
            "
        """
    }

    echo " Deployment completed on ${server}"
}

//testing purpose
// Reusable HTTP test function with retries
def runHttpTest(String url, String testName, int retries = 5, int delay = 5) {
    echo "Running ${testName} on ${url} ..."
    try {
        def status = sh(
            script: """
                for i in \$(seq 1 ${retries}); do
                    STATUS=\$(curl -o /dev/null -s -w "%{http_code}" ${url})
                    if [ "\$STATUS" -eq 200 ]; then
                        echo \$STATUS
                        exit 0
                    fi
                    echo "Retry \$i - Status: \$STATUS"
                    sleep ${delay}
                done
                echo \$STATUS
            """,
            returnStdout: true
        ).trim()

        if (status != "200") {
            error "❌ ${testName} FAILED. HTTP Status: ${status}. Check server or firewall."
        }

        echo "✔️ ${testName} PASSED. HTTP Status: ${status}"

    } catch (err) {
        error "❌ ${testName} ERROR: ${err}"
    }
}

// Reusable integration test function (example)
def runIntegrationTest(String url, String endpoint, String expectedContent) {
    echo "Running Integration Test on ${url}${endpoint} ..."
    try {
        def response = sh(
            script: """
                curl -s ${url}${endpoint}
            """,
            returnStdout: true
        ).trim()

        if (!response.contains("${expectedContent}")) {
            error "❌ Integration Test Failed at ${endpoint}. Expected content not found."
        }

        echo "✔️ Integration Test Passed at ${endpoint}"
    } catch (err) {
        error "❌ Integration Test ERROR: ${err}"
    }
}

def runSanityTest(String url, List<String> elementsToCheck) {
    echo "Running Sanity Test on ${url} ..."
    def pageContent = sh(
        script: "curl -s ${url}",
        returnStdout: true
    ).trim()

    for (el in elementsToCheck) {
        if (!pageContent.contains(el)) {
            error "❌ Sanity Test Failed - Element not found: ${el}"
        } else {
            echo "✔️ Sanity Test Passed - Found element: ${el}"
        }
    }

    echo "✅ All sanity checks passed for ${url}"
}

