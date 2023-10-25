def branch = env.BRANCH_NAME
def build = env.BUILD_NUMBER
def DEBUG = true
def appname ="spring-petclinic"
def DEPLOY = false
def artifactory = "docker.io"   //for example "docker.io/" or "myrepo.example.com/"
def repo = "elevy99927jbdemo" 
def appimage = "${artifactory}${repo}/${appname}"
def apptag = "eyal-${build}"

def kubernetesurl = "https://kubernetes.default.svc"

if (branch == "dev"){
echo "----------  dev   -------"
kubernetesurl = "https://kubernetes.default.svc"
} else if  (branch == "qa"){
echo "----------  qa   -------"
} else if  (branch == "main"){
echo "----------  master   -------"
} else {

}


podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'docker', image: 'gcr.io/kaniko-project/executor:debug-v0.19.0', command: "/busybox/cat", ttyEnabled: true)
  ],
  volumes: [
     configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
  ]) {
    node(POD_LABEL) {
        stage('chackout') {
            container('jnlp') {
            sh '/usr/bin/git config --global http.sslVerify false'
	    checkout scm
          }
        } // end chackout

        stage('build') {
            echo "Building docker image..."
            container('docker') {
                sh "echo OK"
                sh  """
                /kaniko/executor --context=${env.WORKSPACE}/spring-petclinic  \
                --destination ${appimage}:${apptag} \
                --force \
                -v=debug
              """
              //sh "echo docker build -t $appimage --no-cache ."
              //sh "echo docker login $artifactory -u admin -p 1234"
              //sh "echo docker push $appimage"
              //sh "sleep 9999"
            }
        } //end build

        stage('deploy') {
            container('docker') {
                 sh "sleep 9"
	      if (DEPLOY) {
	          sh "sleep 9 "
                echo "***** Doing some deployment stuff *********"
             }  else {
                echo "***** NO DEPLOY - Doing somthing else. Testing? *********"
             }
           }
        } //end deploy
    }
}
