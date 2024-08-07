pipeline {
    agent {
        docker {
            label 'onspot'
            image 'jfrog.joom.it/dockerhub-proxy/golang:1.22-alpine'
            args '-e GOCACHE=/tmp/gocache'
            alwaysPull true
        }
    }
    options {
        ansiColor('xterm')
    }
    stages {
        stage('Build all') {
            matrix {
                axes {
                    axis {
                        name 'GOOS'
                        values 'linux'
                    }
                    axis {
                        name 'GOARCH'
                        values 'amd64', 'arm64'
                    }
                }
                stages {
                    stage('Build') {
                        steps {
                            script {
                                version = env.BRANCH_NAME.replaceFirst('^v', '')
                            }
                            sh "mkdir -p bin_${version}_${GOOS}_${GOARCH}"
                            sh "GOOS=${GOOS} GOARCH=${GOARCH} go build -o bin_${version}_${GOOS}_${GOARCH}/Burrow"
                            sh "tar cvzf burrow_${version}_${GOOS}_${GOARCH}.tar.gz bin_${version}_${GOOS}_${GOARCH}/Burrow"
                            rtUpload serverId: 'artifactory-prod',
                            spec: """{
                                "files": [
                                    {
                                        "pattern": "burrow_${version}_${GOOS}_${GOARCH}.tar.gz",
                                        "target": "burrow-binary-local/"
                                      }
                                   ]
                            }"""
                        }
                    }
                }
            }
        }
    }
}
