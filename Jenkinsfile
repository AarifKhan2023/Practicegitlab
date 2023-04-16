import com.impressico.jenkins.Web_Requests;
import net.sf.json.JSONObject; 

@Library("devOps")_

JSONObject config = init_config('com/impressico/config.json');
List<String> headers = ["Content-Type: application/json", "Authorization: Bearer a9641f06-bc3a-48a7-b253-eeab81db8a13"]
Boolean isOnPremise = true;
Object servers = [publicIpAddressForJenkins];
String user = config.panasonic.deploy[environment].java.user;
String documentroot = config.panasonic.deploy[environment].java.document_root;
String profile = config.panasonic.deploy[environment].java.profile;

String applicationName = applicationName;
String deploymentGroupName = deploymentGroupName;
String s3bucket = s3bucket;
String accessID = accessID;
String secretKEY = secretKEY;

Map slack_input = new TreeMap<>();
slack_input.branch = branch;
slack_input.event = "start";
slack_input.slack_channel = "pancicd"

Map input = new HashMap<>();
input.servers = servers
input.isOnPremise = isOnPremise
input.angularserver = angularDomain;
input.apiserver = serverDomain;
input.mailuser = mailServerUsername;
input.mailpassword = mailServerEmailPassword;
input.wnsclientid = wnsClientId;
input.wnsclientsecret = wnsClientSecret;
input.firebasePath = serverFirebasePath;
input.s3enable = isS3Enabled;
input.environment = environment;
input.profile = profile;
input.documentroot = documentroot;
input.devicebaseurl = serverDomainForDevice;
input.mailDefaultSenderEmail = mailDefaultSenderEmail;
input.mailEnableTLS = mailEnableTLS;
input.mailSeverHost = mailSeverHost;
input.mailServerPort = mailServerPort;

Map git_input = new HashMap<>();
git_input.project = project;
git_input.hosting = hosting;
git_input.branch = branch;

Map serverFirebaseJsonMap = new HashMap<>();
serverFirebaseJsonMap.url = serverFirebaseJson;
serverFirebaseJsonMap.headers = headers;
serverFirebaseJsonMap.path = serverFirebasePath;

Map serverLaunchConfigMap = new HashMap<>();
serverLaunchConfigMap.url = serverLaunchConfig;
serverLaunchConfigMap.headers = headers;
serverLaunchConfigMap.path = serverLaunchConfigPath;

try{ 
    node(node_name){
        environment{
            var_id = '$number'
            number = {Default Value, (String onPremiseid) }
        }
        stage('Pre-Run Slack Notification') {
            slack_notifications(slack_input)
        }
        stage('Clean Workspace') {
        cleanWs()
        }
        panasonic_git_repos(git_input);
        // downloadFile(serverFirebaseJsonMap);
        // downloadFile(serverLaunchConfigMap);
        println panasonic_java_build(environment);
        withCredentials([string(credentialsId:accessID, variable: 'accessid'), string(credentialsId:secretKEY, variable: 'secretkey')]) {
            stage('codeDeploy'){
                step([$class:'AWSCodeDeployPublisher', applicationName:applicationName, awsAccessKey:accessid, awsSecretKey:secretkey, credentials: 'awsAccessKey', deploymentConfig: 'CodeDeployDefault.OneAtATime', deploymentGroupAppspec: false, deploymentGroupName: deploymentGroupName, excludes: '', iamRoleArn: '', includes: 'build/libs/**/*.jar,scripts/*, scripts/$var_id/*, appspec.yml', proxyHost: '', proxyPort: 0, region: 'ap-south-1', s3bucket: s3bucket, s3prefix: '', subdirectory: '', versionFileName: '', waitForCompletion: false])
                }
            }
        }
        slack_input.event = "stop";
        stage('Success- Slack Notification') {
            slack_notifications(slack_input)
        }
}
catch(error){
    slack_input.event = "failure";
	slack_notifications(slack_input);
    throw error

}
finally {
    node(node_name) {
    stage('Final Clean Workspace') {
        cleanWs()
        }
    }
}
