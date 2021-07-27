#!/usr/bin/env groovy
import groovy.json.JsonSlurper 

def getProjects() {
    def reposMap = [:]
    def get = new URL("https://api.github.com/users/liuwu0225/repos").openConnection();
    if(get.getResponseCode().equals(200)) {
        def jsonSlurper = new JsonSlurper()
        def data = jsonSlurper.parseText(get.getInputStream().getText()) 
        for (repo of data) {
            reposMap[repo.name] = repo.git_url
        }
        println(reposMap);
    }
    // get.closeConnection()
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
                    getProjects()
                    // parallel generatePullProjectsStages(Projects.split(','))
                }
            }
        }
    }
}