#!/usr/bin/groovy
@Library('github.com/stakater/fabric8-pipeline-library@helm-node')

String chartPackageName = ""
String chartName = "kibana"

clientsNode(clientsImage: 'stakater/kops-ansible:helm-bundle') {
    container(name: 'clients') {

        stage('Checkout') {
            checkout scm
        }

        stage('Prepare Chart') {
            chartPackageName = prepareChart(chartName)
        }

        stage('Upload Chart') {
            uploadChart(chartName, chartPackageName)
        }
    }
}

def prepareChart(String chartName) {
    result = shOutput """
                cd ${WORKSPACE}/${chartName}
                helm lint > /dev/null
                helm package .
            """

    return result.substring(result.lastIndexOf('/'), result.length)
}

def uploadChart(String chartName, String fileName) {
    sh """
        cd ${WORKSPACE}/${chartName}
        curl -L --data-binary "@${fileName}" http://chartmuseum/api/charts
    """
}

def shOutput(String command) {
    return sh(
        script: """
            ${command}
        """,
        returnStdout: true).trim()
}