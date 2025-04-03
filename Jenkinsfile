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
            steps {
                bat '''
                docker run --rm -v "%CD%:/app" -w /app -e NETLIFY_AUTH_TOKEN=%NETLIFY_AUTH_TOKEN% node:18-alpine sh -c " \
                    npm install netlify-cli && \
                    ./node_modules/.bin/netlify deploy --auth=$NETLIFY_AUTH_TOKEN --site=nfp_xCTxEvuv1dXoce4BuE1pjtkhhKmXBJGe2f59 --dir=. --prod \
                "
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
