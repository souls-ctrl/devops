#!groovy

// final FULL_BUILD = params.FULL_BUILD

final GIT_URL = 'https://github.com/souls-ctrl/devops.git'

final NEXUS_URL = 'nexus:8081/nexus'


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
        sh "mvn sonar:sonar -DskipTests"
        // "echo Analysis static....OK"
      }
    }
  }
}

stage('Artifact upload') {
  node {
    unstash 'artifact'

    def pom = readMavenPom file: 'pom.xml'
    def file = "${pom.artifactId}-${pom.version}"
    sh "echo '${file}'"
    def jar = "target/${file}.war"
    sh "cp pom.xml ${file}.pom"

    nexusArtifactUploader artifacts: [
        [artifactId: "${pom.artifactId}", classifier: '', file: "target/${file}.war", type: 'war'],
        [artifactId: "${pom.artifactId}", classifier: '', file: "${file}.pom", type: 'pom']
      ],
      credentialsId: 'nexus',
      groupId: "${pom.groupId}",
      nexusUrl: NEXUS_URL,
      nexusVersion: 'nexus2',
      protocol: 'http',
      repository: 'ansible-meetup',
      version: "${pom.version}"
  }
}

stage('Deploy') {
  node {
    def pom = readMavenPom file: "pom.xml"
    def repoPath = "${pom.groupId}".replace(".", "/") + "/${pom.artifactId}"
    def version = pom.version
    def artifactUrl = "http://${NEXUS_URL}/repository/ansible-meetup/${repoPath}/${version}/${pom.artifactId}-${version}.war"
    withEnv(["ARTIFACT_URL=${artifactUrl}", "APP_NAME=${pom.artifactId}"]) {
      sh "ansible-galaxy install -vvv -r provision/requirements.xml -p provision/roles/"
      ansiblePlaybook colorized: true,
      credentialsId: 'ssh-jenkins',
      limit: "${HOST_PROVISION}",
      installation: 'ansible',
      inventory: 'provision/inventory.ini',
      playbook: 'provision/playbook.xml',
      sudo: true,
      sudoUser: 'jenkins'
    }
  }
}

