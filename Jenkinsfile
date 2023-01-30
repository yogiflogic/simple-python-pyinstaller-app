node {
    // This step should not normally be used in your script. Consult the inline help for details.
    withDockerContainer('python:2-alpine') {
        // some block
        stage('Build') {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    withDockerContainer('qnib/pytest') {
        // some block
        stage('Test') {
            checkout scm
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    stage('Deploy') {
        // some block
        input message: 'Lanjutkan ke tahap Deploy?'
        checkout scm
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-wilson_oey/sources:/src cdrx/pyinstaller-linux:python2 \'pyinstaller -F add2vals.py\''
        archiveArtifacts artifacts: 'sources/add2vals.py', followSymlinks: false
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-wilson_oey/sources:/src cdrx/pyinstaller-linux:python2 \'rm -rf build dist\''
        sleep time: 1, unit: 'MINUTES'
    }
}
