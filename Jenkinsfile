#!/usr/bin/env groovy

pipeline {
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        choice(name: 'Projects', choices: [
            'FpsAssetLib', 
            'Audio_Menglu',
            'Cafofo_CardsandCasinoVoiceAnnouncer'
        ], description: 'select project(s) to be built')
    }
    stages {
        stage('build') {
            steps {
                echo "${CHOICE} jenkins"
            }
        }
    }
}