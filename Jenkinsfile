pipeline{
    agent any
    
    stages{
        stage('Run Docker Compose'){
            steps {
                sh """ #!/bin/bash
                    git submodule update --init --recursive
                """
                sh """ #!/bin/bash
                    sudo docker-compose -f docker-compose-staging.yml up -d --build
                """
            }
        }  
    }
}
