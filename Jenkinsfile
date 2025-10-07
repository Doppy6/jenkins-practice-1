pipeline{
    agent {
        docker{
            image 'python:3.10'
        }
    }
    
    environment{
        VENV = 'venv'
    }


    stages{
        stage('Setup Environment & Install Dependencies') {
            steps {
                sh 'python -m venv $VENV'
                sh '. $VENV/bin/activate'
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh '. $VENV/bin/activate'
                sh 'pytest tes_app.py'
            }
        }
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps{
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post{
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCES on '${env.BRANCH_NAME}'/nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discordapp.com/api/webhooks/1425159601724194977/IdB-9jkbNzPiI6qNoPONu6YX-xMQX8bu1QCgdVSWTlweeINBStgLwbnOSbx4YTdUK7W_'
                )
            }
        }

        failure {
            script {
                def payload = [
                    content: "❌ Build Failed on '${env.BRANCH_NAME}'/nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discordapp.com/api/webhooks/1425159601724194977/IdB-9jkbNzPiI6qNoPONu6YX-xMQX8bu1QCgdVSWTlweeINBStgLwbnOSbx4YTdUK7W_'
                )
            }
        }
    }
}
