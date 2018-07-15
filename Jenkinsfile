node {
	try{
		stage('checkout the repo') {
			git 'https://github.com/philophilo/MicroService.git'
  		}

        stage('login to dockerhub') {
            withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpass')]) {
                sh 'docker login -u philophilo -p ${dockerpass}'
            }
        }

        stage('Run maven') {
            env.PATH = "${tool 'maven-3.5.3'}/bin:${env.PATH}"
            sh "mvn clean install"
        }

        stage('retrieve artifacts'){
            step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST-*.xml'])
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/site/jacoco', reportFiles: 'index.html', reportName: 'Code coverage', reportTitles: ''])
            archiveArtifacts "target/*.jar"
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
