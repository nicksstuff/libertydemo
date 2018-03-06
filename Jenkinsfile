node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/jglick/simple-maven-project-with-tests.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
   }
   stage('DockerBuild') {
        sh "cd '$JENKINS_HOME/jobs/DEMO Pipeline/workspace/'"
        git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        sh "ls"
         sh "docker build -t test ."
   }
   stage('Deploy') {
         step([$class: 'UCDeployPublisher',
         siteName: 'LOCAL',
         component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'Jenkins',
            delivery: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
                pushVersion: '${BUILD_NUMBER}',
                baseDir: '$JENKINS_HOME/jobs/DEMO Pipeline/workspace/target/',
                fileIncludePatterns: '*.jar',
                fileExcludePatterns: '',
                pushProperties: 'jenkins.server=Local\njenkins.reviewed=false',
                pushDescription: 'Pushed from Jenkins'
            ]
        ]
    ])
   }
}
