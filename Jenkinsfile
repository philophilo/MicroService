node {
	try{
		stage('SCM checkout') {
			git 'https://github.com/philophilo/MicroService.git'
  		}

  		project_path = "Kubernetes-eShop/kubernetes"

        stage('login to dockerhub') {
            echo "========================================"
            withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpass')]) {
                echo "------------------------"
                sh 'docker login -u philophilo -p ${dockerpass}'
            }
        }

        stage('compiler, tester, packager') {
            def mvnHome = tool name:'M3', type: 'maven'
            def mvnCMD = "${mvnHome}/bin/mvn"
            sh "${mvnCMD} clean install"
        }

        stage('Build images and push') {
            sh '. build-images.sh'
        }

	}catch(err){
		notify("Error ${err}")
		currentBuild.result = 'FAILURE'
	}
}
def notify(status){
    emailext(
        to: "phil.kwesiga@gmail.com",
        subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
            <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>"""
    )
}
