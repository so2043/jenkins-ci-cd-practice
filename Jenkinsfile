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
                    // Tomcat 컨테이너 재시작
                    sh 'docker restart tomcat'
                    
                    // Tomcat이 완전히 종료될 때까지 대기
                    sleep 5
                    
                    // 기존 WAR 파일 삭제
                    sh '''
                        docker exec tomcat rm -f /usr/local/tomcat/webapps/webapp-demo.war || true
                        docker exec tomcat rm -rf /usr/local/tomcat/webapps/webapp-demo || true
                    '''
                    
                    // 새 WAR 파일 복사
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
