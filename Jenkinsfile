#!/usr/bin/env groovy
import groovy.json.JsonSlurper 
import groovy.transform.Field
import jenkins.*
import jenkins.model.* 
import hudson.*
import hudson.model.*

@Field def repos = ['ALL']
@Field def reposMap = [:]

def getProjects() {
    // def env = System.getenv()
    // def user = env['GITEA_CREDS_USR']
    // def psw = env['GITEA_CREDS_PSW']
    // print(user)
    // print(psw)
    
    def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
        com.cloudbees.plugins.credentials.Credentials.class,
        Jenkins.instance,
        null,
        null
    ).find{it.id == 'liuwu-gitea'};
    def get = new URL("https://api.github.com/users/liuwu0225/repos").openConnection();
    String userpass = jenkinsCredentials.username + ":" + jenkinsCredentials.password;
    String basicAuth = "Basic " + javax.xml.bind.DatatypeConverter.printBase64Binary(userpass.getBytes());
    get.setRequestProperty ("Authorization", basicAuth);
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
                    // git branch: 'master'
                    //     credentialsId: 'liuwu-gitea'
                    //     url: "${reposMap[job]}" 
                    sh "printenv"
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
                    // TODO
                    // Project build and post script
                }
            }
        }]
    }
}

pipeline {
    agent any
    environment {
        GITEA_CREDS = credentials('liuwu-gitea')
        SYSTEM = "${params.System}"
    }
    parameters {
        choice(name: 'System', choices: ['stg', 'prod'], description: 'select to system to apply this build')
        extendedChoice( 
            defaultValue: '',
            description: 'select project(s) to be built', 
            multiSelectDelimiter: ',', 
            name: 'projects', 
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
                    if(projects == '') {
                        print('Please select at least one project')
                        sh "exit 1"
                    }
                    def projectsList = projects.split(',')
                    if (projectsList.contains('ALL')) {
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