pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        EC2_INSTANCE_ID = 'i-0cd3d8d83c03c27ea'
        REPO_DIR = '/home/ec2-user/aws-devops-cicd-pipeline'
    }

    stages {

        stage('Verify AWS Access') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-jenkins-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-jenkins-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat '''
                        set AWS_DEFAULT_REGION=%AWS_REGION%

                        echo ===== AWS IDENTITY =====
                        aws sts get-caller-identity

                        echo.
                        echo ===== EC2 SSM CONNECTION =====
                        aws ssm describe-instance-information ^
                          --instance-information-filter-list "key=InstanceIds,valueSet=%EC2_INSTANCE_ID%"
                    '''
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-jenkins-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-jenkins-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat '''
                        set AWS_DEFAULT_REGION=%AWS_REGION%

                        echo ===== STARTING AWS DEPLOYMENT =====

                        aws ssm send-command ^
                          --instance-ids %EC2_INSTANCE_ID% ^
                          --document-name "AWS-RunShellScript" ^
                          --parameters commands="git -C %REPO_DIR% pull origin main && docker build -t aws-devops-web:latest %REPO_DIR% && docker rm -f aws-devops-container || true && docker run -d --restart unless-stopped --name aws-devops-container -p 80:80 aws-devops-web:latest && docker ps --filter name=aws-devops-container" ^
                          --query "Command.CommandId" ^
                          --output text > command-id.txt

                        set /p COMMAND_ID=<command-id.txt

                        echo.
                        echo SSM Command ID: %COMMAND_ID%

                        echo.
                        echo ===== WAITING FOR EC2 DEPLOYMENT =====

                        powershell -NoProfile -Command "$status='Pending'; while ($status -notmatch '^(Success|Failed|Cancelled|TimedOut|Undeliverable)$') { Start-Sleep -Seconds 3; $status=(aws ssm get-command-invocation --command-id %COMMAND_ID% --instance-id %EC2_INSTANCE_ID% --query Status --output text).Trim(); Write-Host ('Status: ' + $status) }"

                        echo.
                        echo ===== DEPLOYMENT OUTPUT =====

                        aws ssm get-command-invocation ^
                          --command-id %COMMAND_ID% ^
                          --instance-id %EC2_INSTANCE_ID% ^
                          --query "StandardOutputContent" ^
                          --output text

                        echo.
                        echo ===== ERROR OUTPUT =====

                        aws ssm get-command-invocation ^
                          --command-id %COMMAND_ID% ^
                          --instance-id %EC2_INSTANCE_ID% ^
                          --query "StandardErrorContent" ^
                          --output text

                        echo.
                        echo ===== FINAL STATUS =====

                        aws ssm get-command-invocation ^
                          --command-id %COMMAND_ID% ^
                          --instance-id %EC2_INSTANCE_ID% ^
                          --query "Status" ^
                          --output text
                    '''
                }
            }
        }
    }
}