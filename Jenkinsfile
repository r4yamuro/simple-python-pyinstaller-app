node {
    stage('Checkout') {
        checkout scm
    }
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile -b -d compiled-result sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-result', include: 'compiled-result')
            sh 'ls-la'
        }

    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
    stage('Deliver') {
        env.VOLUME = '${pwd()}/sources:/src'
        env.IMAGE = 'cdrx/pyinstaller-linux:python2'
        dir(env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'pyinstaller -F add2vals.py'"
        }
        archiveArtifacts artifacts: 'sources/dist/add2vals'
        sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} 'rm -rf build dist'"
    }
}