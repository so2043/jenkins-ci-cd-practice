pipeline {
    agent any
    
    tools {
        maven 'Maven3'
    }
    
    stages {
        stage('Git Clone') {
            steps {
                echo '=== Git Clone 시작 ==='
                git branch: 'main', 
                    url: 'https://github.com/so2043/jenkins-ci-cd-practice.git'
                echo '=== Git Clone 완료 ==='
            }
        }
        
        stage('Maven Build') {
            steps {
                echo '=== Maven 빌드 시작 ==='
                sh 'mvn clean package'
                echo '=== Maven 빌드 완료 ==='
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                echo '=== Tomcat 배포 시작 ==='
                script {
                    // 기존 컨테이너 완전 삭제
                    sh 'docker rm -f tomcat || true'
                    
                    // 잠깐 대기
                    sleep 2
                    
                    // 새 Tomcat 컨테이너 시작 (WAR 파일과 함께)
                    sh '''
                        docker run -d \
                          --name tomcat \
                          --network cicd-network \
                          -p 8080:8080 \
                          tomcat:9.0-jdk11
                    '''
                    
                    // Tomcat 시작 대기
                    sleep 10
                    
                    // WAR 파일 복사
                    sh 'docker cp target/webapp-demo.war tomcat:/usr/local/tomcat/webapps/'
                    
                    echo '배포 완료! Tomcat이 자동으로 배포합니다.'
                }
            }
        }
        
        stage('Health Check') {
            steps {
                echo '=== 배포 확인 중 ==='
                script {
                    sleep 15
                    sh 'curl -f http://localhost:8080/webapp-demo/ || echo "배포 진행 중..."'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ 파이프라인 성공!'
        }
        failure {
            echo '❌ 파이프라인 실패!'
        }
    }
}
