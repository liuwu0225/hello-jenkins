#!/usr/bin/env groovy

def getProjects() {
    def response = httpRequest "https://api.github.com/users/liuwu0225/repos"
    println('Status: '+response.status)
    println('Response: '+response.content)
}

def generatePullProjectsStages(projects) {
    return projects.collectEntries {
        ["${it}" : generateStage(it)]
    }
}

def generateStage(job) {
    return {
        stage("Pull Project ${job}") {
            dir("Projects/${job}") {
                git url: "https://github.com/liuwu0225/${job}.git"
            }
        }
    }
}

pipeline {
    agent any
    parameters {
        extendedChoice( 
            defaultValue: '',
            description: 'select project(s) to be built', 
            multiSelectDelimiter: ',', 
            name: 'Projects', 
            quoteValue: false, 
            saveJSONParameterToFile: false, 
            type: 'PT_CHECKBOX', 
            value:'k8s-informer, Temp',
            visibleItemCount: 10)
    }
    stages {
        stage('Pull Projects') {
            steps {
                script {
                    getProjects
                    // parallel generatePullProjectsStages(Projects.split(','))
                }
            }
        }
    }
}