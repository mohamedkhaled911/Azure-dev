pipeline {
    agent any
    environment {
        LINUX_IP = "20.16.63.150"  // ضع هنا IP سيرفر RHEL
        WINDOWS_IP = "192.168.1.101" // ضع هنا IP سيرفر Windows
    }
    properties([
        pipelineTriggers([githubPush()]) // تفعيل Webhook من GitHub
    ])
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/user/repo.git'
            }
        }
        stage('Setup MySQL & Apache on RHEL') {
            agent { label "linux-server" }
            steps {
                sh '''
                sudo yum update -y
                sudo yum install -y httpd mysql-server
                sudo systemctl start httpd
                sudo systemctl enable httpd
                sudo firewall-cmd --add-service=http --permanent
                sudo firewall-cmd --reload
                mysql -u root -e "source src/database/init.sql"
                cp -r src/* /var/www/html/
                '''
            }
        }
        stage('Setup IIS & MySQL on Windows') {
            agent { label "windows-server" }
            steps {
                bat '''
                powershell Install-WindowsFeature -Name Web-Server -IncludeManagementTools
                net start W3SVC
                mysql -u root -e "source src\\database\\init.sql"
                xcopy src C:\\inetpub\\wwwroot /E /Y
                '''
            }
        }
    }
}
