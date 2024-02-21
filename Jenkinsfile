pipeline{
    agent any
    
    stages{
        stage('Get Code'){
            steps{
                //Obtener código del repo
                 git branch: 'develop', url: 'https://github.com/marcusroses/TODO-LIST-AWS.git'
            }
        }
        
        stage('Static'){
            steps{
                sh '/home/ubuntu/todo-aws-list/venv/bin/flake8 --format=pylint --exit-zero src>flake8.out'
                recordIssues tools: [flake8(name: 'Flake8', pattern: 'flake8.out')], qualityGates: [[threshold: 10000000, type: 'TOTAL', unstable: true], [threshold: 100000000, type: 'TOTAL', unstable: false]]
                sh '/home/ubuntu/todo-aws-list/venv/bin/bandit -r src -f custom -o bandit.out -l --msg-template "{abspath}:{line}: {severity}: {test_id}: {msg}"'
                recordIssues tools: [pyLint(name: 'Bandit', pattern: 'bandit.out')], qualityGates: [[threshold: 10000000, type: 'TOTAL', unstable: true], [threshold: 100000000, type: 'TOTAL', unstable: false]]

            }            
        }
        
        stage('Deploy'){
            steps{
                sh 'sam build'
                sh 'sam deploy --config-env staging --no-fail-on-empty-changeset'
                sh '''
                    BASE_URL=$(aws cloudformation describe-stacks --stack-name todo-list-aws --query 'Stacks[0].Outputs[?OutputKey==`BaseUrlApi`].OutputValue' --output text)
                    echo "BASE_URL=$BASE_URL" > env.properties
                    echo "La URL de la API es: $BASE_URL"
                '''
            }            
        } 
        
        stage('Rest Test'){
            steps{
                sh '''
                    /home/ubuntu/todo-aws-list/venv/bin/pytest --junitxml=result-rest.xml test/integration/todoApiTest.py
                '''
            }
        }
        
        stage('Promote') {
            steps {
                sh 'git checkout master'
                sh 'git merge develop'
            }
        }      
    }
}
