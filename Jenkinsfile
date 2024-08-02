#!groovy
pipeline {
	agent { label 'linux' }
    environment {
        CX_VERSION='2.0.70'
        CX_BASE_URI='https://ast.checkmarx.net'
        CX_TENANT='dxc-asod'
        CX_CLIENT_ID=credentials('cx_client_id')
        CX_CLIENT_SECRET=credentials('cx_client_secret')
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    } 
    stages {
        stage("run cxone scan"){
           steps{
            sh "echo 'Downloading CxOne CLI v${CX_VERSION}'"
            sh "wget -O ./cxcli.tar.gz 'https://github.com/Checkmarx/ast-cli/releases/download/${CX_VERSION}/ast-cli_${CX_VERSION}_linux_x64.tar.gz'"
            sh "tar xzvf ./cxcli.tar.gz"
            sh """
                ./cx scan create -s ${env.WORKSPACE} \
                --project-name "${env.JOB_NAME}" \
                --branch "${env.BRANCH_NAME}" \
                --agent 'Jenkins' \
                --base-uri "${CX_BASE_URI}" \
                --tenant "${CX_TENANT}" \
                --client-id "${CX_CLIENT_ID}" \
                --client-secret "${CX_CLIENT_SECRET}" \
                --scan-types "sast,iac-security,sca,api-security" \
                --report-format pdf \
                --report-pdf-email luis-vicente.soto-salinas@dxc.com \
                --wait-delay 60
            """
            }
        }
    } // end main stages
    post {
        always {
            echo 'Clean up Workspace'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'I succeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    } // end post
}  // end pipeline