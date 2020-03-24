def files = ''
def filelist = []
def fileNmExt =''
//def gitPath = '/home/ec2-user/git/apimakr/'
//def apimkrPath = '/home/ec2-user/ctsapimakr/'
def gitPath = '/TestAPI/git/apimakr/'
def apimkrPath = '/TestAPI/ctsapimakr/'
def org = 'manishamishra-eval'
pipeline{
    agent any
    tools {
      maven 'maven'
      nodejs 'node'
      git 'Default'
    }

    environment{
        NODE_PATH = "/var/lib/jenkins/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/node"
        FileSep = "/"

    }

    stages{

        stage('Git Pull'){

            steps{

			sh("echo $USER")
                dir(gitPath){
                   script{
					echo "${env.apigee_deploy_user}"
                    sh('git pull')
                    files = sh(returnStdout:true, script:'git show --pretty="" --name-only').trim()
                    echo "${files}"
                   filelist = files.tokenize("/")
                   if( "${files}" == "Jenkinsfile" ) {
                    skipRemainingStages = true
                   }
                   else{
                    skipRemainingStages = false
                   }
                   }

                }
            }

        }

        stage('APIMaker-CreateProxy'){

        when {
                expression {
                    !skipRemainingStages
                }
            }

            steps{
                script{
                def oasPath = apimkrPath+filelist[1];
                def gitpullpath = gitPath+filelist[0]+"${env.FileSep}"+filelist[1];
				
                sh("cp ${gitpullpath} ${oasPath}")
                dir(apimkrPath){
			sh("who i am")
                   sh("ctsapimakr initialize ${filelist[0]} ${org} ${oasPath}")
                }

            }
            }

        }
        stage('APIMaker-TestProxy'){

        when {
                expression {
                    !skipRemainingStages
                }
            }

            steps{

                echo "test"
            }

        }
        stage('APIMaker-DeployProxy'){

        when {
                expression {
                    !skipRemainingStages
                }
            }

            steps{

               script{

                def deployPath = apimkrPath+filelist[0];
                dir(deployPath){
                   sh("npm run deployTest -u ${env.apigee_deploy_user}:${env.apigee_deploy_user_pwd}")
                }

            }
            }

        }
    }
}
