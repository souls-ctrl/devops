#!groovy

final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      if (FULL_BUILD) {
	echo 'Build....OK'
      }

    }
  }
}


stage('Unit tests') {
  node {
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      echo 'Unit test....OK'
      stash name: "unit-tests"
    }
  }
}



stage('Integration tests') {
  node {
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      echo 'Integration tests....OK'
      stash name: "integ-tests"
    }
  }
}

