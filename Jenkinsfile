node('vss-u ||  vss-u') {

   stage('checkout packer config') {
      // Get some code from a GitHub repository
      // checkout scm
      git url: 'https://github.com/robert-deheer/Packer.git',
         branch: 'fe-proxy'
   }
   
   stage('build ami') {
     sh '''
        export TEMPLATE="environments/base/nginx/template.json"
        export BASEAMI=$(curl -sk "https://test.com:8443/view/lx/lastSuccessfulBuild/console" | grep 'artifact,0,id' | cut -d, -f6 | perl -npe 's/\%!\(PACKER_COMMA\)/\n/g' | grep us-west-2 |  cut -d: -f2)

        sed -ie 's/\.\.\///g' $TEMPLATE

        jq " .builders[].source_ami=\"$BASEAMI\" \
        | .builders[].region=\"us-west-2\" \
        | .builders[].vpc_id=\"vpc-2b8bab46\" \
        | .builders[].subnet_id=\"subnet-ce4e8a18\" \
        | .builders[].tags.BaseAMI=\"$BASEAMI\" \
        | .builders[].tags.BaseHash=\"`git rev-parse --verify HEAD`\" \
        | .builders[].tags.BaseRepo=\"`git config --get remote.origin.url`\" \
        | .builders[].ami_regions=\"us-west-2, us-east-1\" " $TEMPLATE > temp1
        cat temp1
        jq " .provisioners[0] |= (.+ { \
        \"type\": \"ansible-local\", \
        \"playbook_dir\": \"ansible-provision\", \
        \"playbook_file\": \"ansible-provision/fe-proxy.yml\", \
        \"inventory_file\": \"ansible-provision/hosts\", \
        \"extra_arguments\": [\"{{user \`ansible_vars\`}}\"] }) " temp1 > temp2
        jq " .provisioners[2] |= (.+ { \
        \"source\": \"ansible-provision/roles/fe-proxy/tests/serverspec\" }) " temp2 > temp3
        cat temp3
        mv temp3 $TEMPLATE
     '''

     withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        credentialsId: '4885fed4-c315-42430-b90b-7b566776da37',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
           sh '''
              /Users/build/tools/biz.neustar.jenkins.plugins.packer.PackerInstallation/Packer_0.10.1/packer \
              build -machine-readable environments/base/nginx/template.json
           '''
        }
     }
}
