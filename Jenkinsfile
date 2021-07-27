#!/usr/bin/env groovy
// import com.cwctravel.hudson.plugins.extended_choice_parameter.ExtendedChoiceParameterDefinition

pipeline {
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        extendedChoice( 
            defaultValue: '',
            description: 'select project(s) to be built', 
            multiSelectDelimiter: ',', 
            name: 'Projects', 
            quoteValue: false, 
            saveJSONParameterToFile: false, 
            type: 'PT_CHECKBOX', 
            value:'ALL,FpsAssetLib,Audio_Menglu,Cafofo_CardsandCasinoVoiceAnnouncer',
            visibleItemCount: 5)
    }
    
    stages {
        stage('Pull Projects') {
            parallel {
                stage('Pulling Project k8s-informer') {
                    steps {
                        dir('Projects/k8s-informer') {
                            git url: 'https://github.com/liuwu0225/k8s-informer.git'
                        }
                    }
                }
                stage('Pulling Project Temp') {
                    steps {
                        dir('Projects/Temp') {
                            git url: 'https://github.com/liuwu0225/Temp.git'
                        }
                    }
                }
            }
        }
    }
}