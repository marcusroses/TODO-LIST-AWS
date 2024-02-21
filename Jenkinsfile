pipeline{
    agent any
    
    stages{
        stage('Get Code'){
            steps{
                //Obtener código del repo
                 git branch: 'master', url: 'https://github.com/marcusroses/TODO-LIST-AWS.git'
            }
        }
        
        stage('Deploy'){
            steps{
                sh 'sam build'
                sh 'sam deploy --config-env production --no-fail-on-empty-changeset'
                sh '''
                    BASE_URL=$(aws cloudformation describe-stacks --stack-name todo-list-aws-production --query 'Stacks[0].Outputs[?OutputKey==`BaseUrlApi`].OutputValue' --output text)
                    echo "BASE_URL=$BASE_URL" > env2.properties
                    echo "La URL de la API es: $BASE_URL"
                '''
            }            
        } 
        
        stage('Rest Test'){
            steps{
                sh 'ls'
                sh '/home/ubuntu/todo-aws-list/venv/bin/pytest --junitxml=result-rest.xml test/integration/todoApiTest2.py'
            }
        }
    }
}
