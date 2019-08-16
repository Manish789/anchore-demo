#!groovy
pipeline{
    environment{
            PROJECT = "anchore"
            ECRURL = "http://220536757961.dkr.ecr.ap-south-1.amazonaws.com"
            ECRCRED ="ecr:ap-south-1:aws-ecr-cred"
    }
    
    agent none{
	    stage('image pull'){
            sh("eval \$(aws ecr get-login --no-include-email | sed 's|https://||')")
		    docker.withRegistry(ECRURL, ECRCRED){
                docker.image(PROJECT).pull()
        }
	}
    }
}
