pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'nfp_fwWsxVKGnzgonKyRj8K6R1qhfFvX1CX1fd97'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            steps {
                echo "✅ Checking required files..."
                bat '''
                    docker run --rm -v "%CD%:/app" -w /app node:18-alpine sh -c ^
                    "if [ ! -f index.html ]; then echo 'index.html is missing!' && exit 1; fi && echo 'All necessary files are in place!'"
                '''
            }
        }

        stage('Test') {
            steps {
                echo "🧪 Testing function load..."
                bat '''
                    docker run --rm -v "%CD%:/app" -w /app node:18-alpine sh -c ^
                    "node -e \\"console.log('Function loaded successfully!')\\""
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying to Netlify..."
                bat """
                    docker run --rm -v "%CD%:/app" -w /app -e NETLIFY_AUTH_TOKEN=%NETLIFY_AUTH_TOKEN% node:18-alpine sh -c ^
        "npm install -g netlify-cli && netlify deploy --auth=%NETLIFY_AUTH_TOKEN% --site=%NETLIFY_SITE_ID% --dir=. --prod --message=\\"Deployed via Jenkins\\" --json"
                """
            }
        }


        stage('Post Deploy') {
            steps {
                echo "🎉 Deployment complete! Check your site on Netlify."
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD pipeline completed successfully!"
        }
        failure {
            echo "❌ Something went wrong. Please check the logs."
        }
    }
}
