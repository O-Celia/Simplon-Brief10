pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhubaccount')
        SONAR_ORGANIZATION_KEY = 'simplon-celiaouedraogo'
        SONAR_TOKEN = credentials('sonarcloud')
    }
    stages {
        stage('Pré-Cleanup') {
            steps {
                // Clean before build
                cleanWs()
                echo "Building ${env.JOB_NAME}..."
            }
        }
        stage('Cloning the git') {
            steps {
                sh('''
                git clone https://github.com/Simplon-CeliaOuedraogo/Brief8-celia.git
                ''')
            }
        }
        stage('Build image') {
            steps {
                sh('''
                    cd Brief8-celia
                    docker build -t vote-app .
                ''')
            }
        }
        stage('Login and Push image on DockerHub') {
            steps {
                sh('''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    cd Brief8-celia
                    PATCH=\044(cat azure-vote/main.py | grep -E "^ver = \\"[0-9.]+\\"\\$"|awk -F\\" {'print $2'})
                    docker tag vote-app celiaoued/vote-app:\044PATCH
                    docker push celiaoued/vote-app:\044PATCH
                ''')
            }
        }
        stage('Change tag yaml and launch vote site') {
            steps {
                sh('''
                git clone https://github.com/Simplon-CeliaOuedraogo/brief7-yaml.git app
                TAG=\044(curl -sSf https://registry.hub.docker.com/v2/repositories/celiaoued/vote-app/tags |jq '."results"[0]["name"]'| tr -d '"')
                sed -i "s/TAG/\044{TAG}/" ./app/vote.yaml
                kubectl apply -f ./app
                ''')
            }
        }
    }
    post {
        always {
            // Deconnexion Docker
            sh 'docker logout'
        }
    }
}
