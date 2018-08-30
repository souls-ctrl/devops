#!groovy

final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      echo FULL_BUILD
      if (FULL_BUILD) {
	echo 'Build....OK'
      }

    }
  }
}

if (FULL_BUILD) {
  stage('Unit tests') {
    node {
      withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
	 echo 'Unit test....OK'
	stash name: "unit-tests"
      }
    }
  }
}

if (FULL_BUILD) {
  stage('Integration tests') {
    node {
      withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
	echo 'Integration tests....OK'
	stash name: "integ-tests"
      }
    }
  }
}

