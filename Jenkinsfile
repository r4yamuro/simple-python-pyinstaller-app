node {
    stage('Checkout') {
        checkout scm
    }
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', include: 'sources/**')
        }

    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
    stage ('Approval') {
        def userInput = input message: 'Deploy ke production?', ok: 'Deploy', parameters: [booleanParam(name: 'DEPLOY', description: 'Deploy or cancel?', defaultValue: false)]
        if (userInput.DEPLOY) {
            echo 'Memulai Deployment...'
        } else {
            error 'Deployment telah dibatalkan'
        }
    }
    stage('Deploy') {
        env.VOLUME = "${pwd()}/sources:/src"
        env.IMAGE = "cdrx/pyinstaller-linux:python2"
        dir(env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'pyinstaller -F add2vals.py'"
        }
        archiveArtifacts artifacts: 'sources/dist/add2vals'
        sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'rm -rf build dist'"
        sh "sleep 60"
    }
}