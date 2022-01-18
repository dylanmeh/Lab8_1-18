def props = [:]
@Library("lab3") _ 
properties([pipelineTriggers([eventTrigger(jmespathQuery("labs.lab8='8'"))])])
podTemplate(containers: [
    containerTemplate(name: 'maven', image: 'maven:3.8.1-jdk-11', command: 'sleep', args: '99d')
]) {
    node(POD_LABEL) {
        stage('SCM Checkout') {
            git url: 'https://github.com/dylanmeh/Lab6_1-10.git', branch: 'main'

        }
        stage('declare properties file') {
            container('maven') {
                stage('declare the properties file') {
                    props = readProperties(file: 'build.properties') 
                }
            }
        }
        stage('first conditional stage') {
            container('maven') {
                stage('enable unit testing when event is prod') {
                    if (getTriggerCauseEvent.getTriggerCauseEvent() == 'true') {
                        println 'enabling unit testing'    
                    }
                }
            }    
        }        
        stage('second conditional stage') {
            container('maven') {
                stage('disable unit testing when event is dev') {
                    if (getTriggerCauseEvent.getTriggerCauseEvent() == 'false') {
                        println 'user disabled unit testing'
                    }
                }
            }    
        }    
        stage('buildstart') {
            container('maven') {
                stage('buildStart Time Stage') {
                    if ("${props["buildStart"]}" == 'true') {
                        buildStart()
                    }    
                }
            }
        }
        stage('build') {
            container('maven') {
                git 'https://github.com/dylanmeh/Lab2_Java-App.git'
                stage('build') {
                    if ("${props["mvnbuild"]}" == 'true') {
                        sh 'mvn -B -DskipTests clean package'
                    }    
                }
            }
        }        
        stage('test') {
            container('maven') {
                stage('test') {
                    if ("${props["mvntest"]}" == 'true') {
                        sh 'mvn test'
                        junit 'target/surefire-reports/*.xml'
                    }    
                }
            }
        }
        stage('deploy') {
            container('maven') {
                stage('deploy') {
                    if ("${props["mvndeploy"]}" == 'true') {
                        sh './scripts/deliver.sh'
                    }    
                }           
            }
        }
        stage('email notification') {
            if (currentBuild.currentResult == 'SUCCESS') {
                buildResultsEmail("Successful")
            } else {
                buildResultsEmail("Failure")
            }
        }
    }
}