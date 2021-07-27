#!/usr/bin/env groovy
// import com.cwctravel.hudson.plugins.extended_choice_parameter.ExtendedChoiceParameterDefinition

// def repos = ['k8s-informer', 'Temp']

// def parallelStagesMap = repos.collectEntries {
//     ["${it}" : generateStage(it)]
// }

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
                    parallel generatePullProjectsStages(Projects.split(','))
                }
            }
        }
    }
}