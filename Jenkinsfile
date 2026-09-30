pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    parameters {
        string(
            name: 'PETCLINIC_REPO',
            defaultValue: 'https://github.com/spring-projects/spring-petclinic.git',
            description: 'Git repository Jenkins should clone for the application source.'
        )
        string(
            name: 'PETCLINIC_BRANCH',
            defaultValue: 'main',
            description: 'Branch to clone from the PetClinic repository.'
        )
        booleanParam(
            name: 'DEPLOY',
            defaultValue: false,
            description: 'Reserved for the later optional Ansible deployment stage.'
        )
        string(
            name: 'SONAR_TOKEN',
            defaultValue: '',
            description: 'SonarQube token. Leave empty to skip the scan.'
        )
    }

    stages {
        stage('Checkout PetClinic') {
            steps {
                dir('spring-petclinic') {
                    git branch: params.PETCLINIC_BRANCH,
                        url: params.PETCLINIC_REPO
                }
            }
        }

        stage('Verify Checkout') {
            steps {
                sh '''
                    test -f spring-petclinic/pom.xml
                    git -C spring-petclinic remote get-url origin
                    git -C spring-petclinic rev-parse --short HEAD
                '''
            }
        }

        stage('Build With Maven') {
            steps {
                dir('spring-petclinic') {
                    sh './mvnw clean compile'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('spring-petclinic') {
                    sh './mvnw test'
                }
            }
        }

        stage('Package App') {
            steps {
                dir('spring-petclinic') {
                    sh './mvnw package -DskipTests'
                    sh 'ls -lh target/*.jar'
                }
            }
        }

        stage('SonarQube Scan') {
            when {
                expression { return params.SONAR_TOKEN?.trim() }
            }
            steps {
                dir('spring-petclinic') {
                    sh '''
                        ./mvnw sonar:sonar \
                            -Dsonar.host.url=http://sonarqube:9000 \
                            -Dsonar.projectKey=spring-petclinic \
                            -Dsonar.projectName="Spring PetClinic" \
                            -Dsonar.token="${SONAR_TOKEN}"
                    '''
                }
            }
        }

        stage('Deploy With Ansible') {
            when {
                expression { return params.DEPLOY }
            }
            steps {
                sh '''
                    PETCLINIC_JAR="$(find "${WORKSPACE}/spring-petclinic/target" -maxdepth 1 -type f -name '*.jar' ! -name '*.original' | head -n 1)"
                    test -n "${PETCLINIC_JAR}"
                    ansible-playbook \
                        -i /workspace/cicd-pipeline/ansible/inventory.ini \
                        /workspace/cicd-pipeline/ansible/deploy-petclinic.yml \
                        --extra-vars "petclinic_jar=${PETCLINIC_JAR}"
                '''
            }
        }
    }
}
