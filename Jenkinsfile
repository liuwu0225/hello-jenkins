#!/usr/bin/env groovy
// import com.cwctravel.hudson.plugins.extended_choice_parameter.ExtendedChoiceParameterDefinition

pipeline {
    agent any
    environment {
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    parameters {
        // choice(name: 'Projects', choices: [
        //     'ALL'
        //     'FpsAssetLib', 
        //     'Audio_Menglu',
        //     'Cafofo_CardsandCasinoVoiceAnnouncer'
        // ], description: 'select project(s) to be built')
        extendedChoice defaultValue: 'blue,green,yellow,blue', description: '', descriptionPropertyValue: 'blue,green,yellow,blue', multiSelectDelimiter: ',', name: 'favColor', quoteValue: false, saveJSONParameterToFile: false, type: 'PT_MULTI_SELECT', value: 'blue,green,yellow,blue', visibleItemCount: 5

    }

    
    stages {
        stage('build') {
            steps {
                echo "${Projects} jenkins"
            }
        }
    }
}