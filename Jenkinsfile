#!/usr/bin/env groovy

pipeline {
    // agent { docker { image 'node:6.3' } }
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    }
    stages {
        stage('build') {
            steps {
                echo "${CHOICE} jenkins"
            }
        }
    }
}