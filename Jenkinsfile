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
			sh "npm version minor"

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
        stage('Build and Push docker image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USER', passwordVariable: 'PWD')]){
                    sh "docker build -t snegi2020/myapp:${IMAGE_NAME} ."
                    sh "echo ${PWD} | docker login -u ${USER} --password-stdin"
                    sh "docker push snegi2020/myapp:${IMAGE_NAME}"
                }
            }
        }
	stage('commit version update') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PWD')]) {
                        sh "git remote set-url origin https://${USER}:${PWD}@https://github.com/sn2020/solution-jenkins.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:jenkins-jobs'
                    }
                }
            }
    	}
    }
}
