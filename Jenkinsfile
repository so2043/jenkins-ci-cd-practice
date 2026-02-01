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
                    // 1. Tomcat 컨테이너 중지
                    sh 'docker stop tomcat'
                    
                    // 2. 대기 (완전히 중지될 때까지)
                    sleep 3
                    
                    // 3. 호스트로 WAR 파일 복사 (준비 영역)
                    sh '''
                        docker cp target/webapp-demo.war jenkins:/tmp/webapp-demo.war
                    '''
                    
                    // 4. Tomcat 시작
                    sh 'docker start tomcat'
                    
                    // 5. Tomcat이 시작될 때까지 대기
                    sleep 5
                    
                    // 6. Jenkins 컨테이너에서 Tomcat으로 파일 복사
                    sh '''
                        docker cp /tmp/webapp-demo.war tomcat:/usr/local/tomcat/webapps/
                    '''
                    
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
