#!/usr/bin/env groovy
import groovy.json.JsonSlurper 
import groovy.transform.Field

@Field def repos = ['ALL']
@Field def reposMap = [:]
@Field def githubAddress = 'https://github.com/liuwu0225'

def getProjects() {
    def get = new URL("https://api.github.com/users/liuwu0225/repos").openConnection();
    if(get.getResponseCode().equals(200)) {
        def jsonSlurper = new JsonSlurper()
        def data = jsonSlurper.parseText(get.getInputStream().getText()) 
        for (repo in data) {
            repos.push(repo.name)
            reposMap[repo.name] = repo.git_url
        }
        return repos.join(',')
    }
}

def generatePullProjectsStages(repos) {
    return repos.collectEntries {
        ["${it}" : generateStage(it)]
    }
}

def generateStage(repo) {
    return {
        stage("Pull Project ${repo}") {
            dir("Projects/${repo}") {
                // git url: "${reposMap[job]}""
                echo "${reposMap[repo]}"
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
                    def projectsList = Projects.split(',')
                    if (projectsList.contains('ALL')) {
                        repos = repos.remove('ALL')
                    } else {
                        repos = projectsList
                    }
                    parallel generatePullProjectsStages(repos)
                }
            }
        }

        // stage('Build Project and Upload Assets') {
        //     steps {
        //         script {
        //             def projectsList = Projects.split(',')
        //             if (projectsList.contains('ALL')) {
        //                 repos.remove('ALL')
        //                 parallel generatePullProjectsStages(repos)
        //             } else {
        //                 parallel generatePullProjectsStages(projectsList)
        //             }
        //         }
        //     }
        // }
    }
}