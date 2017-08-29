#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@master')

def failIfNoTests = ""
try {
  failIfNoTests = ITEST_FAIL_IF_NO_TEST
} catch (Throwable e) {
  failIfNoTests = "false"
}

def itestPattern = ""
try {
  itestPattern = ITEST_PATTERN
} catch (Throwable e) {
  itestPattern = "*KT"
}

def versionPrefix = ""
try {
  versionPrefix = VERSION_PREFIX
} catch (Throwable e) {
  versionPrefix = "1.0"
}

def canaryVersion = "${versionPrefix}.${env.BUILD_NUMBER}"
//def utils = new io.fabric8.Utils()

node('jenkinsci-node-172-30-109-131') {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("perlyn/hellonode")
    }

#   stage('Test image') {
#       /* Ideally, we would run a test framework against our image.
#        * For this example, we're using a Volkswagen-type approach ;-) */
#
#        app.inside {
#            sh 'echo "Tests passed"'
#        }
#    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'hub.docker.credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
