// node {
//     // This step should not normally be used in your script. Consult the inline help for details.
//     withDockerContainer('python:2-alpine') {
//         // some block
//         stage('Build') {
//             checkout scm
//             sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//         }
//     }
//     withDockerContainer('qnib/pytest') {
//         // some block
//         stage('Test') {
//             checkout scm
//             sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
//             junit 'test-reports/results.xml'
//         }
//     }
//     stage('Deploy') {
//         // some block
//         input message: 'Lanjutkan ke tahap Deploy?'
// //         checkout scm
// //         sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-wilson_oey/sources:/src cdrx/pyinstaller-linux:python2 \'pyinstaller -F add2vals.py\''
// //         archiveArtifacts artifacts: 'sources/add2vals.py', followSymlinks: false
// //         sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-wilson_oey/sources:/src cdrx/pyinstaller-linux:python2 \'rm -rf build dist\''
//         sleep time: 1, unit: 'MINUTES'
//     }
// }

pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

         stage('Pausing') {
            steps {
                sleep(time: 2, unit: 'MINUTES')
            }
        }
        
        stage('Deliver') { 
            agent any
            environment { 
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) { 
                    unstash(name: 'compiled-results') 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
}
