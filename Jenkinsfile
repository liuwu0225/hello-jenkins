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
        extendedChoice( 
            defaultValue: 'One,Two,Three,Four',
            description: '', 
            multiSelectDelimiter: ',', 
            name: 'SAMPLE_EXTENDED_CHOICE', 
            quoteValue: false, 
            saveJSONParameterToFile: false, 
            type: 'PT_CHECKBOX', 
            value:'One,Two,Three,Four,Five,Six,Seven,Eight,Nine,Ten', 
            visibleItemCount: 10)
    }
}

    
    stages {
        stage('build') {
            steps {
                echo "${Projects} jenkins"
            }
        }
    }
}