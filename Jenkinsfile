//stages {
   stage('checkout...') {
      node {
         // Mark the code checkout 'stage'....
         //stage 'Checkout'

         // Get some code from a GitHub repository
         checkout scm

         stash includes: '*', name: 'root'

      }
   }
   stage('say howdy...') {
      node {
          //stage 'Hello'
          unstash 'root'
          def hello = "Hello, world"
          echo hello

      }
   }
//}
