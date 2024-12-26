pipeline {
    agent any

    environment {
        // URL et identifiants de SonarQube configurés dans Jenkins (utilisation des credentials de Jenkins)
        SONAR_SERVER = 'http://192.168.53.239:9000' // Adresse de votre serveur SonarQube
        SONAR_TOKEN = credentials('project-token') // Le token SonarQube ajouté dans les credentials de Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo '=========== Checking out code ================='
                    git url: 'https://github.com/ilham1239/sonar_test.git', branch: "main"
                }
            }
        }

        stage('Run Containers with Docker Compose') {
            steps {
                echo "========== Pulling and running the containers ========"
                bat 'docker-compose up -d'
                echo '===== LOG ==== exit-code1: %ERRORLEVEL%'
                echo "Containers are running"
            }
        }

        stage('Code Analysis with SonarQube') {
            steps {
                script {
                    echo "========== Running SonarQube Analysis ========"
                    
                    // Vérifiez que le scanner SonarQube est disponible sur le PATH
                    bat 'sonar-scanner --version'

                    // Lancez le scan SonarQube
                    bat """
                        sonar-scanner ^
                        -Dsonar.projectKey=sonar_test ^
                        -Dsonar.sources=. ^
                        -Dsonar.host.url=${SONAR_SERVER} ^
                        -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        // Si nécessaire, vous pouvez ajouter ici d'autres étapes (comme Trivy, pour scanner les containers).
    }

    post {
        always {
            script {
                echo "============= Turning OFF containers ==============="
                bat 'docker-compose down'
                echo '===== LOG ==== docker-compose exit-code2: %ERRORLEVEL%'

                echo "============= Cleaning up the workspace ==============="
                bat 'del /q /s * && for /d %%p in (*) do rmdir "%%p" /s /q'

                echo "============= Removing the .git folder ==============="
                bat 'rmdir /s /q .git'
            }
        }
    }
}
