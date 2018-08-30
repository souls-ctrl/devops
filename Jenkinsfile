#!groovy

final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      if (FULL_BUILD) {
	sh "echo Build....OK"
      }

    }
  }
}

if (FULL_BUILD) {
  stage('Unit tests') {
    node {
      withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
	sh "echo Unit test....OK"
	stash name: "unit-tests"
      }
    }
  }
}

if (FULL_BUILD) {
  stage('Integration tests') {
    node {
      withEnv(["PATH+MAVEN=${tool 'maven'}/bin]) {
	sh "echo Integration tests....OK"
	stash name: "integ-tests"
      }
    }
  }
}

