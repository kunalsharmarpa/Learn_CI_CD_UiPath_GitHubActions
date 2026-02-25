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
       // UIPATH_SCOPES        = "OR.Assets OR.Assets.Write OR.BackgroundTasks OR.BackgroundTasks.Read OR.BackgroundTasks.Write OR.Execution OR.Execution.Read OR.Execution.Write OR.Folders OR.Folders.Read OR.Folders.Write OR.Jobs OR.Jobs.Read OR.Jobs.Write OR.Machines.Read OR.Monitoring OR.Monitoring.Read OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSetExecutions.Read OR.TestSetExecutions.Write OR.TestSets OR.TestSets.Read OR.TestSets.Write"
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
                    cliPath: 'C:/Users/kajal/Downloads/UiPath.CLI.Windows.25.10.9',
                    packagePath: "Output\\${env.BUILD_NUMBER}",
                    createProcess: true,
                    processName: 'CICD_Automation',
                    entryPointPaths: 'Main.xaml',
                    orchestratorAddress: 'https://cloud.uipath.com/',
                    orchestratorTenant: 'DefaultTenant',
                    folderName: 'CICD',
                    environments: '',
                    ignoreLibraryDeployConflict: false,
                    traceLevel: 'None',

                    credentials: [
                        $class: 'ExternalAppAuthenticationEntry',
                        accountForApp: 'kunal_cloud',
                        applicationId: '80804fca-caa2-4674-85a0-de014debc694',
                        applicationSecret: 'o%c%t~$T95*4Ca(ddp#Fwm!cD)4sFWAP6#T??0IXkOyCKdO*7#8$DJjmy?*o6eui',     // ← no quotes, no interpolation
                        //applicationScope: 'OR.Assets OR.Assets.Write OR.BackgroundTasks OR.BackgroundTasks.Read OR.BackgroundTasks.Write OR.Execution OR.Execution.Read OR.Execution.Write OR.Folders OR.Folders.Read OR.Folders.Write OR.Jobs OR.Jobs.Read OR.Jobs.Write OR.Machines.Read OR.Monitoring OR.Monitoring.Read OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSetExecutions.Read OR.TestSetExecutions.Write OR.TestSets OR.TestSets.Read OR.TestSets.Write',
                        //identityUrl: 'https://cloud.uipath.com/identity_'
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
