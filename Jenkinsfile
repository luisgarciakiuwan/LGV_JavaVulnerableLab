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
        echo 'LGV - Analysing project '
        script {
					withCredentials([usernamePassword(credentialsId: '03578a00-2bed-4e5d-970c-35cf49d9d3ba', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agent.cmd -c -s \"${WORKSPACE}\" -n \"${JOB_NAME}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\"", returnStatus: true) 
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