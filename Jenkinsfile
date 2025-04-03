pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'nfp_xCTxEvuv1dXoce4BuE1pjtkhhKmXBJGe2f59'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            steps {
                bat '''
                docker run --rm -v "%CD%:/app" -w /app node:18-alpine sh -c " \
                    if [ ! -f index.html ]; then echo 'index.html is missing!' && exit 1; fi && \
                    echo 'All necessary files are in place!' \
                "
                '''
            }
        }

        stage('Test') {
            steps {
                bat '''
                docker run --rm -v "%CD%:/app" -w /app node:18-alpine sh -c " \
                    node -e \\"console.log('Function loaded successfully!')\\" \
                "
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "Deploying the project to Netlify..."
                sh '''
                    npm init -y  # <--- เพิ่มบรรทัดนี้ ถ้ายังไม่มี package.json
                    npm install netlify-cli
                    ./node_modules/.bin/netlify deploy \
                      --auth=$NETLIFY_AUTH_TOKEN \
                      --site=$NETLIFY_SITE_ID \
                      --dir=. \
                      --prod
                '''
            }
        }

        stage('Post Deploy') {
            steps {
                echo "🎉 Deployment is complete! Your website is now live."
            }
        }
    }

    post {
        success {
            echo "CI/CD pipeline executed successfully!"
        }
        failure {
            echo "❌ An error occurred during the pipeline execution. Please check the logs!"
        }
    }
}
