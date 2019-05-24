def files = ''
 def filelist = []
def fileNmExt =''
def gitPath = 'C:\Manisha\personal\APIMakerDemo\apimakr\'
def apimkrPath = 'C:\Manisha\personal\'
def org = 'manishamishra2-eval'
pipeline{
    agent any
    tools {
      maven 'maven'
      nodejs 'node'
      git 'Default'
    }

    environment{

        NODE_PATH = "C:\Users\manisha.mishra\AppData\Roaming\npm\node_modules"
        FileSep = "/"

    }

    stages{


        stage('Git Pull'){

            steps{

                dir(gitPath){
                   script{
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
                   sh("npm run deployTest")
                }

            }
            }

        }
    }
}
