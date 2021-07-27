#!/usr/bin/env groovy

pipeline {
    // agent { docker { image 'node:6.3' } }
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        string(name: 'Greeting', defaultValue: 'Hello', description: 'How should I greet the world?')
    }
    stages {
        stage('build') {
            steps {
                echo "${Greeting} jenkins"
            }
        }
    }
}