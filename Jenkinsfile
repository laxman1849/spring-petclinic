pipeline{
    agent { label 'JDK-17-JAVA'}
    //triggers { pollSCM ('* * * * *') }
    parameters {
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean'], description: 'Maven Goal')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/laxman1849/spring-petclinicpr12.git',
                    branch: 'release'
            }
        }
        stage('package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('build') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar',
                                 onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml'

            }
        }
        stage('craeting folder') {
            steps {
                sh "mkdir -p /tmp/${JOB_NAME}/${BUILD_ID}"
                sh "cp -r **/spring-petclinic-*.jar /tmp/${JOB_NAME}/${BUILD_ID}"
                sh "aws s3 sync /tmp/${JOB_NAME}/${BUILD_ID} s3://narayana-s3-bucket --acl public-read-write"
            }
        }
        stage ('deployment') {
            steps {
                sh 'ansible-playbook -i hosts ./springpetclinic.yaml'
            }
        }
    }

}