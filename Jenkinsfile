node {

    stage('Clean Workspace') {
        cleanWs()
    }

    stage('Checkout') {
        checkout scm
    }

    stage('Commit Sign-Off Check') {
        def commitMsg = sh(
            script: "git log -1 --pretty=%B",
            returnStdout: true
        ).trim()

        if (!commitMsg.contains("Signed-off-by")) {
            error("Commit Sign-off missing")
        }
    }

    stage('Build & Test') {
        sh 'mvn clean verify'
    }

    stage('SonarQube Analysis') {
        withSonarQubeEnv('SonarQube') {
            sh 'mvn sonar:sonar'
        }
    }

    stage('Quality Gate') {
        timeout(time: 5, unit: 'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                error "Quality Gate Failed"
            }
        }
    }

    stage('Notification') {
        mail to: 'nk6638844@gmail.com',
             subject: "Build ${currentBuild.currentResult}: ${env.JOB_NAME}",
             body: "Build ${currentBuild.currentResult} - Check console output"
    }
}
