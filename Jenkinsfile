def branch = env.BRANCH_NAME
def build = env.BUILD_NUMBER
def DEBUG = true
def appname ="spring-petclinic"
def DEPLOY = false
def artifactory = "docker.io"   //for example "docker.io/" or "myrepo.example.com/"
def repo = "choopcloud" 
def appimage = "${artifactory}/${repo}/${appname}"
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
      containerTemplate(name: 'docker', image: 'gcr.io/kaniko-project/executor:debug', command: "/busybox/cat", ttyEnabled: true)
  ],
  volumes: [
     configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
  ])  {
    node(POD_LABEL) {
        stage('chackout') {
            container('jnlp') {
            sh '/usr/bin/git config --global http.sslVerify false'
	    checkout scm
          }
        } // end chackout

        stage('build') {
            container('docker') {
		sh "echo OK"
		sh """
                /kaniko/executor --context=git://github.com/aghubs/spring-petclinic.git  \
                --dockerfile=Dockerfile \
                --destination=${appimage}:${apptag} \
	        --insecure \
                --skip-tls-verify  \
                --force \
                -v=debug
                """
	}
      } //end build

        stage('deploy') {
            container('docker') {
	      if (DEPLOY) {
                echo "***** Doing some deployment stuff *********"
             }  else {
                echo "***** NO DEPLOY - Doing somthing else. Testing? *********"
             }
           }
        } //end deploy
    }
}
