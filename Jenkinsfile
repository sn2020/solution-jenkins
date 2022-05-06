pipeline {
    agent any
    tools {
        nodejs "node"
    }
    stages {
        stage('increment version') {
            steps {
                script {
                    dir("app") {
                        def packageJson = readJSON file: 'package.json'
                        def version = packageJson.version

                        env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                    	echo "${env.IMAGE_NAME}"
			}

                }
            }
        }
    	stage('Run tests') {
            steps {
               script {
                    dir("app") {
                        sh "npm install"
                        sh "npm run test"
                    }
               }
            }
        }
    }
}
