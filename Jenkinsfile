pipeline {
    agent {
        label 'built-in'
    }
    tools {
        git 'git-install'
        maven 'maven-install'
    }

    stages {
        stage('env-variables') {
            steps {
                script {
                    env.SQL_LINK = 'database-2.ccl0s6sc4bn5.us-east-1.rds.amazonaws.com'
                }
            }
        }
        stage('git pull') {
            steps {
                cleanWs()
                checkout scm
                echo "$WORKSPACE"
            }
        }
        stage('mvn clean install') {
            steps {
                script {
                    sh 'mvn clean install'
                }
            }
        }
        stage('copy .war file') {
            steps {
                script {
                    def fileName = '/mnt/tools/apache-tomcat-9.0.106/webapps/LoginWebApp.war'
                    if (fileExists(fileName)) {
                        sh "rm -rf ${fileName}"
                    }
                    sh "cp ${WORKSPACE}/target/LoginWebApp.war /mnt/tools/apache-tomcat-9.0.106/webapps/"
                }
            }
        }
        stage('login to mysql and add user table in test database') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'mysql-cred', usernameVariable: 'USER_NAME', passwordVariable: 'MYSQL_PASSWORD')]) {
                        sh """
                    mysql -h $SQL_LINK -u $USER_NAME -p$MYSQL_PASSWORD < mysql.sql
                    
                """
                    }
                }
            }
        }
    }
}
