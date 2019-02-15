// Scripted Pipeline
node('master') {
    stage('Source') { //get code from gitlab
        git 'http://gitlab.example.com/root/windows.git'
    }
    
    stage('Build') { // build docker container in the jenkins host
        bat 'docker build -t dtr.example.local/admin/windows:latest .' 
    }

    stage('Push') { // Login into DTR
        bat 'docker login -u admin -p redhat12  dtr.example.local && docker push dtr.example.local/admin/windows:latest'
    }

    try {   //  remove the service if it already exists, if it doesn't exists it will continue
          stage('Destroy') {
                bat 'env.cmd && docker service rm dockerwin' }
    }
    catch(e) {
              build_ok = false
              echo e.toString()
    } 

    stage('Deploy') { // deploy to docker ee 
        bat 'env.cmd && docker login -u admin -p redhat12 dtr.example.local && docker service create --name dockerwin --network new-hrm-network --publish target=80,published=8010 --label com.docker.ucp.mesh.http=external_route=http://dockerwin.example.local,internal_port=80 --dns=192.168.12.10 dtr.example.local/admin/windows:latest'
    }  
}