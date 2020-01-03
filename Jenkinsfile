def jobnameparts = JOB_NAME.tokenize('/') as String[]
def jobconsolename = jobnameparts[0]
pipeline {
  agent any
  stages {
    stage('Build Stage') {
      steps {
        echo 'LGV - Building project '
      }
    }
    stage('Analysis Stage ') {
      steps {
        echo "LGV - Analysing project ${JOB_BASE_NAME} ${jobconsolename}"
        script {
           def branch_name = JOB_BASE_NAME
           def app_name = jobconsolename
           echo "LGV - branch_name [ ${JOB_BASE_NAME} ] app_name [ ${jobconsolename} ]"
           switch(branch_name) {
                   case "lgv-branch":
                   	echo "In the dev branch we whould execute a delivery anlysis"
                   	withCredentials([usernamePassword(credentialsId: '79d08bad-643a-43a9-a662-537b8710cfcb', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agentpp.cmd  -s \"${WORKSPACE}\" -n \"${app_name}\" -as completeDelivery -cr MyCr -bn \"${branch_name}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\"", returnStatus: true) 
						switch(returnCode){
								case 0:
									break
								case 14:
									currentBuild.result = 'UNSTABLE'
									break
								default:
									currentBuild.result = 'NOT_BUILT'
						}
					}
                   	break
                   default:
                   	echo "In the master branch we whould exec a baseline"
                   	withCredentials([usernamePassword(credentialsId: '79d08bad-643a-43a9-a662-537b8710cfcb', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agentpp.cmd DELIVERY -c -s \"${WORKSPACE}\" -n \"${JOB_BASE_NAME}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\"", returnStatus: true) 
						switch(returnCode){
								case 0:
									break
								case 14:
									currentBuild.result = 'UNSTABLE'
									break
								default:
									currentBuild.result = 'NOT_BUILT'
						}
					}
                   	break
               } 
        }

           
        script {
        
					withCredentials([usernamePassword(credentialsId: '03578a00-2bed-4e5d-970c-35cf49d9d3ba', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agentkk.cmd -c -s \"${WORKSPACE}\" -n \"${JOB_BASE_NAME}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\"", returnStatus: true) 
						switch(returnCode){
								case 0:
									break
								case 14:
									currentBuild.result = 'UNSTABLE'
									break
								default:
									currentBuild.result = 'NOT_BUILT'
						}
					}
				}
      }
    }
  }
}