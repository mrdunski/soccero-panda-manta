node ('docker') {
    stage('test project') {
        checkout scm
        sh 'chmod +x ./gradlew'
        def result = sh (script: './gradlew clean test', returnStatus: true)
        step([$class: 'JUnitResultArchiver', healthScaleFactor: 1000.0,
                     testResults: '**/test-results/**/*.xml'])
        sh "exit $result"
     }

    stage('build docker') {
        withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'password', usernameVariable: 'user')]) {
            sh "docker login -u $user -p $password"
            checkout scm
            sh 'chmod +x ./gradlew'
            sh "./gradlew dockerPushProjectVersion dockerPushLatest generateK8sFile -Pv=`date -u +%Y%m%d-%H%M%S`"
            archiveArtifacts 'build/deployment.yaml'
            stash includes: 'build/deployment.yaml', name: 'deployment.yaml'
        }
    }
}