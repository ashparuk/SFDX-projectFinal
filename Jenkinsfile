#!groovy
import groovy.json.JsonSlurperClassic
node {
    def BUILD_NUMBER= "1"
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="paruk.ashraf@gmail.com"
    def SFDC_HOST ="https://coroash-dev-ed.lightning.force.com/"
    def JWT_KEY_CRED_ID ="9760b0ba-49e3-4328-b74c-e2503a8cd84c"
    def CONNECTED_APP_CONSUMER_KEY="3MVG98_Psg5cppyZuv5b8ElD38zklwSKE1o_B5XeS.BL.ySYM_LsbUnfWJc6IHeDm.cWK_gkakh5.J0Dh0dfr"

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    //def toolbelt = tool 'sfdx'

    stage('Checkout git Source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }
    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploy to SF') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }
			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -w 10 -d force-app/main/default/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:retrieve -u ${HUB_ORG} -x ./force-app/main/default//package.xml -w 10"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}