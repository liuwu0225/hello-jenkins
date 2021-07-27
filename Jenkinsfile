#!/usr/bin/env groovy

pipeline {
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        choice(name: 'CHOICE', choices: [
            'FpsAssetLib', 
            'Audio_Menglu',
            'Cafofo_CardsandCasinoVoiceAnnouncer'
        ], description: 'Projects')
    }
    stages {
        stage('build') {
            steps {
                echo "${CHOICE} jenkins"
            }
        }
    }
}