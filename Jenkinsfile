#!/usr/bin/env groovy

//noinspection GroovyAssignabilityCheck
pipeline {
    agent any
    stages {
        stage('Install Dependencies') {
            steps {
                checkout scm
                sh 'npm install'
                stash includes: 'node_modules/**', name: 'node_modules'
            }
        }
        stage('Run ESLint') {
            steps {
                checkout scm
                unstash 'node_modules'
                sh 'npm run lint'
            }
        }
        stage('Build Artifacts') {
            steps {
                //noinspection GroovyAssignabilityCheck
                parallel(
                    'Development Bundle': {
                        checkout scm
                        unstash 'node_modules'
                        sh 'npm run build'
                        archiveArtifacts 'dist/mapbox-gl-circle.js'
                    },
                    'Production Bundle': {
                        checkout scm
                        unstash 'node_modules'
                        sh 'npm run prepare'
                        archiveArtifacts 'dist/mapbox-gl-circle.min.js'
                    },
                    'API Docs': {
                        checkout scm
                        unstash 'node_modules'
                        sh 'npm run docs'
                        archiveArtifacts 'API.md'

                    },
                    'Docker Image': {
                        checkout scm
                        sh 'docker build -t docker.smithmicro.io/mapbox-gl-circle .'
                        sh 'docker save docker.smithmicro.io/mapbox-gl-circle | gzip - > mapbox-gl-circle.docker.tar.gz'
                        archiveArtifacts 'mapbox-gl-circle.docker.tar.gz'
                    }
                )
            }
        }
    }
}
