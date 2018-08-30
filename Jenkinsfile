#!groovy

// final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

stage('Build') {
  node {
    git GIT_URL
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      def pom = readMavenPom file: 'pom.xml'
      sh "mvn -B versions:set -DnewVersion=${pom.version}-${BUILD_NUMBER}"
      sh "mvn -B -Dmaven.test.skip=true clean package"
      stash name: "artifact", includes: "target/soccer-stats-*.war"
    }
  }
}


stage('Unit tests') {
  node {
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      sh "mvn -B clean test"
      stash name: "unit-tests", includes: "target/surefire-reports/**"
    }
  }
}



stage('Integration tests') {
  node {
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      sh "mvn -B clean verify -Dsurefire.skip=true"
      stash name: "integ-tests", includes: "target/failsafe-reports/**"
    }
  }
}

stage('Static analysis') {
  node {
    withEnv(["PATH+MAVEN=${tool 'maven'}/bin"]) {
      withSonarQubeEnv('sonarqube') {
        unstash 'integ-tests'
        unstash 'unit-tests'
        sh "mvn sonar:sonar -DskipTests -Dsonar.host.url=http://localhost:9000"
      }
    }
  }
}

