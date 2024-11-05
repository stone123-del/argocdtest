pipeline {
  agent any
  stages {
    stage('git scm update') {
      steps {
        git url: 'https://github.com/stone123-del/argocdtest.git', branch: 'master'
      }
    }
    stage('delivery and deployment') {
      steps {
        sh '''
        ansible master -m copy -a "src=playbook.yml dest=/root/playbook.yml" --become #  become루트권한
        now=$(date +%y%m%d%H%M) # 변수설정
        sudo docker build -t ilovesnows:test:${now} .
        sudo docker push brian24/keduitlab:${now}
        ansible node -m shell -a "sudo docker pull ilovesnows:test:${now}"
        ansible master -m shell -a "sudo kubectl create deploy web-${now} --replicas=3 --port=80 --image=ilovesnows:test:${now}"
        ansible master -m shell -a "sudo kubectl expose deploy web-${now} --type=LoadBalancer --port=80 --target-port=80 --name=web-${now}-svc"
        '''
      }
    }
  }
}
