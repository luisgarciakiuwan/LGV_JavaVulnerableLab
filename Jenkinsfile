import groovy.json.JsonSlurper
def jobnameparts = JOB_NAME.tokenize('/') as String[]
def jobconsolename = jobnameparts[0]
private static Object getJson(String path) {
	String token = "?changeRequest=MyCr&label=41"
	String base = "https://api.kiuwan.com/apps/JavaVulnerableLab/deliveries"
	String apiString =base + token

	URL apiUrl = new URL(apiString)
	HttpURLConnection myURLConnection = (HttpURLConnection)apiUrl.openConnection();
	String cred="lgv.appsec:password.0";
	String authStr = Base64.getEncoder().encodeToString( cred.getBytes());
	myURLConnection.setRequestProperty ("Authorization", "Basic " + authStr);
	return new JsonSlurper().parse(myURLConnection.inputStream)
}

static int getAuditResult(String project, String changeRequest, String deliveryLabel) {
    println( "project: [" + project + "]")
    println( "changeRequest: [" + changeRequest + "]")
    println( "deliveryLabel: [" + deliveryLabel + "]")
	try {
	def json = getJson("https://api.kiuwan.com/apps/" + java.net.URLEncoder.encode(project,"UTF-8") + "/deliveries?changeRequest=" + java.net.URLEncoder.encode(changeRequest,"UTF-8") + "&label=" + java.net.URLEncoder.encode(deliveryLabel, "UTF-8"))

	println( "------" )
	println( json.auditResult.overallResult )
	println( json.auditResultURL )
	println( json )
	return 0
	} catch (e) {
	//Connection error
	//System.err.println "Connection to Kiuwan cannot be established to get delivery information: " + e.getMessage()
	//e.printStackTrace()
	return 1
	}
	
	return 0
}


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
                   	getAuditResult( ${app_name}, "MyCr", ${BUILD_NUMBER} )
                   	echo "hasta luego lucas"
                   	
                   	withCredentials([usernamePassword(credentialsId: 'c413cf58-94b6-488e-a0aa-cac2f3d5badc', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agent.cmd  -s \"${WORKSPACE}\" -n \"${app_name}\" -as completeDelivery -cr MyCr -bn \"${branch_name}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\" > salida.txt", returnStatus: true) 
						switch(returnCode){
								case 0: // sucessfull
									break
								case 10: // audit fail
									result = readFile('salida.txt').trim()
									echo " ---------------"
									echo " [ ${result} ] "
									echo " ---------------"
									idx = result.indexOf( "Analysis results URL:" )
									audit_url = result.substring( idx+21, idx+21+100)
									echo " ---------------"
									echo " [ ${audit_url} ] "
									echo " ---------------"
									currentBuild.result = 'ABORTED'
									break
								default:
									currentBuild.result = 'FAILURE'
						}
					}
					
                   	break
                   	
                   default:
                   	echo "In the master branch we whould exec a baseline"
                   	withCredentials([usernamePassword(credentialsId: '79d08bad-643a-43a9-a662-537b8710cfcb', 
					passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
					def returnCode = bat(script: "C:/LGV/kla_kw/KiuwanLocalAnalyzer/KiuwanLocalAnalyzer/bin/agent.cmd -c -s \"${WORKSPACE}\" -n \"${app_name}\" -l ${BUILD_NUMBER} -wr --user \"$USERNAME\" --pass \"$PASSWORD\"", returnStatus: true) 
						switch(returnCode){
								case 0: // sucessfull
									break
								default:
									currentBuild.result = 'FAILURE'
						}
					}
                   	break
               } 
        }

      }
    }
  }
}