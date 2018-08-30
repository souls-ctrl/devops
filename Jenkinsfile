#!groovy

final FULL_BUILD = params.FULL_BUILD

final GIT_URLS = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'Maven'}/bin"]) {
      if (FULL_BUILD) {
	sh "echo hello world"
      }

    }
  }
}

