stage('Configure') {
        ecrRepository = '220536757961.dkr.ecr.ap-south-1.amazonaws.com/anchore'
        registryCredential = 'aws-ecr-cred'
}
node {
  def app
  def dockerfile
  def anchorefile
  def repotag

  try {
    stage('Checkout') {
      // Clone the git repository
      checkout scm
      def path = sh returnStdout: true, script: "pwd"
      path = path.trim()
      dockerfile = path + "/Dockerfile"
      anchorefile = path + "/anchore_images"
    }

    stage('Build') {
      // Build the image and push it to a staging repository
      repotag = ecrRepository + ":${BUILD_NUMBER}"
      docker.withRegistry('https://220536757961.dkr.ecr.ap-south-1.amazonaws.com', 'ecr:ap-south-1:aws-ecr-cred') {
        app = docker.build(repotag)
        app.push()
      }
    }

    stage('Parallel') {
      parallel Test: {
        app.inside {
            sh 'echo "Dummy - tests passed"'
        }
      },
      Analyze: {
        writeFile file: anchorefile, text: '220536757961.dkr.ecr.ap-south-1.amazonaws.com' + "/" + repotag + " " + dockerfile
        anchore name: anchorefile, engineurl: '172.19.0.6:8228/v1', engineCredentialsId: registryCredential , annotations: [[key: 'added-by', value: 'jenkins']]
      }
    }
  } finally {
    stage('Cleanup') {
      // Delete the docker image and clean up any allotted resources
      sh script: "docker rmi " + repotag
    }
  }
}
