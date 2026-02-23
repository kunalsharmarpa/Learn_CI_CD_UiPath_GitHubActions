pipeline {
    agent any

    environment {
        MAJOR = '1'
        MINOR = '0'

        // Orchestrator Cloud Info
        UIPATH_ORCH_URL          = "https://cloud.uipath.com/"
        UIPATH_ORCH_ACCOUNT_NAME = "Kunal_Cloud"          // Logical Name
        UIPATH_ORCH_TENANT_NAME  = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME  = "CICID"

        // External App OAuth
        UIPATH_CLIENT_ID     = credentials('UIPATH_CLIENT_ID')
        UIPATH_CLIENT_SECRET = credentials('UIPATH_CLIENT_SECRET')
        UIPATH_SCOPES        = "OR.Folders OR.Folders.Read OR.Folders.Write OR.Execution OR.Execution.Read OR.Execution.Write"
    }

    stages {

        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub Branch ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building..with ${WORKSPACE}"
                UiPathPack(
                    outputPath: "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: false,
                    traceLevel: 'None'
                )
            }
        }

        stage('Test') {
            steps {
                echo 'Testing workflow...'
            }
        }

        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to UAT"
                UiPathDeploy(
                    packagePath: "Output\\${env.BUILD_NUMBER}",
                    createProcess: true,
                    processName: 'CICD_Automation',
                    entryPointPaths: 'Main.xaml',
                    orchestratorAddress: 'https://cloud.uipath.com/kunal_cloud/DefaultTenant',
                    orchestratorTenant: 'DefaultTenant',
                    folderName: 'CICD',
                    environments: '',
                    ignoreLibraryDeployConflict: false,
                    traceLevel: 'None',

                    credentials: [
                        $class: 'ExternalAppAuthenticationEntry',
                        accountForApp: 'kunal_cloud',
                        applicationId: '80804fca-caa2-4674-85a0-de014debc694',
                        applicationSecret: '_%A#Tf0xLjiWd^O*rTFaNSK*v6~d%Qzt2%nlhm2wXmm3JEDaN#rKorgHBI5Uy$Na',     // ← no quotes, no interpolation
                        applicationScope: 'OR.Folders OR.Folders.Read OR.Folders.Write OR.Execution OR.Execution.Read OR.Execution.Write',
                        identityUrl: 'https://cloud.uipath.com/identity_'
                    ]

                ) 
              
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploy to Production'
            }
        }
    }

    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            cleanWs()
        }
    }
}
