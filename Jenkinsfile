node {
  def mvnHome
  stage('Preparation') { // for display purposes
    // Get some code from a GitHub repository
    git credentialsId: 'ec8087f2-8cc0-4d65-99bc-5efdd0085d6f', url: 'https://github.com/niklaushirt/libertydemo.git'
    // Get the Maven tool.
    // ** NOTE: This 'M3' Maven tool must be configured
    // **       in the global configuration.
    mvnHome = tool 'M3'
  }
  stage('CI-Build') {
    // Run the maven build
    if (isUnix()) {
      sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore package"
    } else {
      bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
    }
  }
  stage('CI-Docker-Build') {
    sh "cd '$JENKINS_HOME/jobs/DEMO Pipeline/workspace/'"
    sh "ls"
    //docker.withRegistry('http://mycluster.icp:8500', 'admin') {
    //sh "sudo docker build -t test ."
    def customImage = docker.build("liberty_demo:demo")
    //customImage.push('demo')
    //}
  }
  stage('CI-Push-To-UrbanCode') {
    step([$class: 'UCDeployPublisher',
      siteName: 'LOCAL',
      component: [
        $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
        componentName: 'Jenkins',
        delivery: [
          $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
          pushVersion: '${BUILD_NUMBER}',
          baseDir: '$JENKINS_HOME/jobs/${env.JOB_NAME}/workspace/',
          fileIncludePatterns: '*.*',
          fileExcludePatterns: '',
          pushProperties: 'jenkins.server=Local\njenkins.reviewed=false',
          pushDescription: 'Pushed from Jenkins'
        ]
      ]
    ])
  }
}
