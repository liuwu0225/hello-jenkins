#!/usr/bin/env groovy
import groovy.json.JsonSlurper 
import groovy.transform.Field
import jenkins.*
import jenkins.model.* 
import hudson.*
import hudson.model.*

@Field def repos = ['ALL']
@Field def reposMap = [:]
@Field def credsMap = [:]

def loadCredentials() {
    def credsArr = credentials.split(";")
    credsArr.each {
        def data = it.split(",")
        String key = "${data[0]}-${data[1]}-${data[2]}"
        credsMap[key] = "${data[3]}-${data[4]}"
    }
}

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
                    // sh "printenv"
                }
            }
        }]
    }
}

def getSystem(system) {
    switch(system) {
        case 'stg':
            return '1';
        default:
            return '1';
    }
}

def generateBuildProjectsStages(repos, environment, region) {
    repos.each {
        credKey = "${region}-${environment}-Infinity_Shoot"
        print credKey
        print credsMap
        credsMap.each {
            print it.key instanceof String
        }
        print credsMap.(credKey as String)
    }
    return repos.collectEntries {
        ["${it}" : {
            stage("Pull Project ${it}") {
                dir("Projects/${it}") {
                    // TODO
                    // Project build and post script
                    sh """
                        echo "OK"
                    """
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
        choice(name: 'Loca', choices: ['Stging', 'Production'], description: 'select to system to apply this build')
        choice(name: 'Region', choices: ['CN', 'EU'], description: 'select to system to apply this build')
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
                    loadCredentials()
                    parallel generateBuildProjectsStages(repos, Loca, Region)
                }
            }
        }
    }
}

@Field def credentials = '''
CN,Stging,Infinity_Shoot,developer@yahaha.games,Dev4fun;
CN,Stging,Lover_Catcher,developer@yahaha.games,Dev4fun;
CN,Stging,Tutorial_1,developer@yahaha.games,Dev4fun;
CN,Stging,Desert,developer@yahaha.games,Dev4fun;
CN,Stging,Nature_Week,developer@yahaha.games,Dev4fun;
CN,Stging,Tutorial_2,developer@yahaha.games,Dev4fun;
CN,Stging,TerrainL,developer@yahaha.games,Dev4fun;
CN,Stging,Forest,developer@yahaha.games,Dev4fun;
CN,Stging,Empty_Scene,developer@yahaha.games,Dev4fun;
EU,Production,Infinity_Shoot,creator@yahaha.com,Hello1234;
EU,Production,Lover_Catcher,creator@yahaha.com,Hello1234;
EU,Production,Tutorial_1,creator@yahaha.com,Hello1234;
EU,Production,Desert,creator@yahaha.com,Hello1234;
EU,Production,Nature_Week,creator@yahaha.com,Hello1234;
EU,Production,Tutorial_2,creator@yahaha.com,Hello1234;
EU,Production,TerrainL,creator@yahaha.com,Hello1234;
EU,Production,Forest,creator@yahaha.com,Hello1234;
EU,Production,Empty_Scene,creator@yahaha.com,Hello1234;
EU,Production,Basic_Shapes,creator@yahaha.com,Hello1234;
EU,Production,Construction_Corps,creator@yahaha.com,Hello1234;
EU,Production,Yahaha_Fall,creator@yahaha.com,Hello1234;
EU,Production,Audio_Menglu,creator@yahaha.com,Hello1234;
EU,Production,Viking_Ice_World,creator@yahaha.com,Test1234;
EU,Production,Modern_Supermarket,info@novadev.com,Test1234;
EU,Production,Japanese_Apartment,suppport@vonoffice.com,Test1234;
EU,Production,House_Furniture_Pack,AidenGames@gmail.com,Test1234;
EU,Production,Vectorian_interior,contact@alex.com,Test1234
'''