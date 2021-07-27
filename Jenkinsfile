#!/usr/bin/env groovy
import groovy.json.JsonSlurper 
import groovy.transform.Field

@Field def reposMap = [:]
@Field def githubAddress = 'https://github.com/liuwu0225'

def getProjects() {
    def get = new URL("https://api.github.com/users/liuwu0225/repos").openConnection();
    if(get.getResponseCode().equals(200)) {
        def repos = []
        def jsonSlurper = new JsonSlurper()
        def data = jsonSlurper.parseText(get.getInputStream().getText()) 
        for (repo in data) {
            repos.push(repo.name)
            reposMap[repo.name] = repo.git_url
        }
        return repos.join(',')
    }
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
                // git url: "https://github.com/liuwu0225/${job}.git"
                echo "${githubAddress}/${job}.git"
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
            value: getProjects(),
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