#!/usr/bin/env groovy
import groovy.json.JsonSlurper 
import groovy.transform.Field

@Field def repos = ['ALL']
@Field def reposMap = [:]

def getProjects() {
    def env = System.getenv()
    def user = env['GITEA_CREDS_USR']
    def psw = env['GITEA_CREDS_PSW']
    print(user)
    print(psw)
    def get = new URL("https://${user}:${psw}@api.github.com/users/liuwu0225/repos").openConnection();
    if(get.getResponseCode().equals(200)) {
        def jsonSlurper = new JsonSlurper()
        def data = jsonSlurper.parseText(get.getInputStream().getText()) 
        for (repo in data) {
            repos.push(repo.name)
            reposMap[repo.name] = repo.clone_url
        }
        return repos.join(',')
    }
}

def generatePullProjectsStages(repos) {
    return repos.collectEntries {
        ["${it}" : {
            stage("Pull Project ${it}") {
                dir("Projects/${it}") {
                    // git url: "${reposMap[job]}""
                    echo "${reposMap[it]}"
                }
            }
        }]
    }
}

def generateBuildProjectsStages(repos) {
    return repos.collectEntries {
        ["${it}" : {
            stage("Pull Project ${it}") {
                dir("Projects/${it}") {
                    // git url: "${reposMap[job]}""
                    echo "Projects/${it}"
                }
            }
        }]
    }
}

pipeline {
    agent any
    environment {
        GITEA_CREDS = credentials('liuwu-gitea')
    }
    parameters {
        choice(name: 'System', choices: ['stg', 'prod'], description: 'select to system to apply this build')
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
                    if(Projects == '') {
                        print('Please select at least one project')
                        sh "exit 1"
                    }
                    def projectsList = Projects.split(',')
                    print("***************")
                    print(Projects == '')
                    print(projectsList)
                    print(projectsList.size())
                    if(projectsList.size() == 0) {
                        print('Please select at least one project')
                        exit -1
                    } else if (projectsList.contains('ALL')) {
                        repos.remove('ALL')
                    } else {
                        repos = projectsList
                    }
                    parallel generatePullProjectsStages(repos)
                }
            }
        }

        stage('Build Project and Upload Assets') {
            steps {
                script {
                    parallel generateBuildProjectsStages(repos)
                }
            }
        }
    }
}