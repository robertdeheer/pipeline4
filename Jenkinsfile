node {
   // Mark the code checkout 'stage'....
   stage 'Checkout'

   // Get some code from a GitHub repository
   checkout scm

   stash includes: '*', name: 'root'

}

node {
    stage 'Hello'
    unstash 'root'
    def hello = readFile('world.txt')
    echo hello

}
