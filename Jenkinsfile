node {
    stage('Build') {
        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
    stage('Test') {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
    }
}