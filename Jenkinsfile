#!groovy

// final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      def pom = readMavenPom file: 'pom.xml'
      sh "mvn -B version:set -DnewVersion=${pom.version}-${BUILD_NUMBER}"
      sh "mvn -B -Dmaven.test.skip=true clean package"
      stash name: "artifact", includes: "target/soccer-stats-*.war"
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

