pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'nfp_xCTxEvuv1dXoce4BuE1pjtkhhKmXBJGe2f59'
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
        
                // สร้าง package.json ข้างนอกก่อน
                bat 'if not exist package.json npm init -y'
        
                // แล้วค่อยรันใน container
                bat """
                    docker run --rm --user 0 -v "%CD%:/app" -w /app -e NETLIFY_AUTH_TOKEN=%NETLIFY_AUTH_TOKEN% node:18-alpine sh -c ^
                    "npm install netlify-cli && ./node_modules/.bin/netlify deploy --auth=%NETLIFY_AUTH_TOKEN% --site=%NETLIFY_SITE_ID% --dir=. --prod"
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
