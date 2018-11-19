pipeline 
  {
    environment 
       {
          registry = 'ansiblepocacr.azurecr.io/ansible_poc'
          registryCredential = 'acrcredential'
	  dockerImage = ''
       }
    agent none
    stages 
       {
         stage('Build') 
	    {
	       agent 
		    { docker 
		         { image 'maven:3-jdk-8-alpine' 
		           args '-v /data/jenkins/tools/maven/.m2:/root/.m2'
                         }   
		    }
                steps 
		    {		
                        git credentialsId: 'GitHub', url: 'https://github.com/Tonyamoljose/InformationCenter.git'
                        sh 'mvn clean deploy sonar:sonar'      
                    }
            }
         stage('Building image') 
            {
               steps
		   {
                      script {
                              dockerImage = docker.build registry + ":$BUILD_NUMBER"
                             }
                   }
            }
	 stage('Deploy Image to ACR') 
	    {
                steps
		    {
                      script {                     
			      docker.withRegistry( 'https://ansiblepocacr.azurecr.io', 'acrcredential' ) 
			        { dockerImage.push() }
                             }
                    }
             }
	 stage('Deploy Application to AKS') 
	    {
                steps
		    {
                      script {   
                               app = docker.image registry + ":$BUILD_NUMBER"
	                       docker.withRegistry( 'https://ansiblepocacr.azurecr.io', 'acrcredential' ) 
		                         {app.pull()
		                          sh "kubectl apply -f ." }
		             }
		    }    
            }
       }
   }
