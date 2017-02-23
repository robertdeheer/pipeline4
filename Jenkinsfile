node('vjs-ub14-002 ||  vjs-ub14-001') {
   stage('checkout packer config') {
      // Get some code from a GitHub repository
      // checkout scm
      git url: 'https://v-usmi-e-github1.eng.fireeye.com/robert-deheer/Packer',
         branch: 'fe-proxy'
   }
   stage('build ami') {
      sh "packer build -var='base_name=myapp' -var='project_id=evandbrown17' -var='revision=${rev}' packer.json"
   }
}
