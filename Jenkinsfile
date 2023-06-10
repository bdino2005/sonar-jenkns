pipeline {
    agent any
    
    parameters {
        string(name: 'TEMPLATE_NAME', defaultValue: 'my-template', description: 'Name of the template')
        text(name: 'TEMPLATE_CONTENT', defaultValue: '', description: 'Content of the template')
    }
    
    stages {
        stage('Create Template') {
            steps {
                script {
                    // Validate template name
                    def templateName = params.TEMPLATE_NAME.replaceAll('[^a-zA-Z0-9_-]', '')
                    if (templateName.isEmpty()) {
                        error('Invalid template name. Only alphanumeric characters, hyphens, and underscores are allowed.')
                    }
                    
                    // Check if template already exists
                    if (fileExists("templates/${templateName}.txt")) {
                        error("Template '${templateName}' already exists. Please choose a different name.")
                    }
                    
                    // Create the template file
                    writeFile(file: "templates/${templateName}.txt", text: params.TEMPLATE_CONTENT)
                    
                    echo "Template '${templateName}' created successfully!"
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube scanner
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner'
                }
            }
        }
        
        stage('Build and Push Docker Image') {
            environment {
                DOCKER_REGISTRY = 'docker.example.com'
                DOCKER_IMAGE_NAME = 'my-template'
                DOCKER_IMAGE_TAG = 'latest'
            }
            
            steps {
                // Build Docker image
                sh 'docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} .'
                
                // Push Docker image to registry
                sh 'docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}'
                
                echo "Docker image ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} built and pushed successfully!"
            }
        }
    }
}
