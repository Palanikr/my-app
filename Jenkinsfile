node{
   stage('GIT Checkout'){
     git 'https://github.com/palanikr/my-app.git'
   }
   stage('Maven Build'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube QualityCheck') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Docker Build'){
   sh 'docker build -t palanikumar7/myweb:0.0.2 .'
   }
   stage('Docker Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u palanikumar7 -p ${dockerPassword}"
    }
   sh 'docker push palanikumar7/myweb:0.0.2'
   }
   stage('Nexus Push'){
   sh "docker login -u admin -p admin123 18.232.50.103:8083"
   sh "docker tag palanikumar7/myweb:0.0.2 18.232.50.103:8083/palani:1.0.0"
   sh 'docker push 18.232.50.103:8083/palani:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deploy'){
   sh 'docker run -d -p 8090:8080 --name tomcattest palanikumar7/myweb:0.0.2' 
   }
}
}
