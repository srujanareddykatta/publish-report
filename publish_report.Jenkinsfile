def GIT_REPO_URL = 'https://code.devsnc.com/dev/zion.git'
//def GIT_REPO_URL = 'https://code.devsnc.com/kesavan-athimoolam/citrus_poc1.git'
def RPM_VERSION = null                    
def GIT_USER_FULL_NAME = 'Kesavan Athimoolam'
def GIT_USER_EMAIL = 'kesavan.athimoolam@servicenow.com'

pipeline{
    
    agent any
    parameters {
        string defaultValue: 'srujanareddy.katta', description: 'comma seperated email recipients (eg: kesavan.athimoolam,rex.pullan) ', name: 'EMAIL_CSV', trim: true
        string defaultValue: 'master', description: 'Eg: master  or feature/release/1.3.42', name: 'BRANCH', trim: true
    }
      
        stage('Generate list of recent commits to branch'){
            steps{
                
                script{
        		
					   
	                def htmlstringHEADER = '<STYLE>BODY, TABLE, TD, TH, P,tr { font-family: Arial, Calibri, Verdana, Helvetica, sans serif ;padding:6px }</style><style> tr:nth-child(even) {background-color: #ffffff;}</STYLE>'
	                
	                
	               	def htmlstring = htmlstringHEADER + "<BODY><p><a href=\"https://code.devsnc.com/dev/zion\">dev/zion</a><p><h2><p>"+"Recent commits in <i>"+ params.BRANCH+ "</i> branch  in the past "+ params.DAYS +" days    : "+ "<p><p>  "
	                htmlstring = htmlstring + '<table style="border-collapse:collapse;background-color:#efefef;border-width:0;border-style:solid;border-color:black"><thead style="padding:5px;background-color:#aeaeae;font-weight:bold;"><td>CommitID</td><td>Committer</td><td>Date</td><td>Commit message</td></thead>'+ HTML_commits_in_master + '</table><p><p><p></BODY>'
	                print htmlstring
	               
	                writeFile file:'changelist.html', text: htmlstring
	                archiveArtifacts 'changelist.html' 
	                
	                write_python_script()
                    assert(fileExists('send_email.py'))
                    assert(fileExists('changelist.html'))
                     
                        
                    withCredentials([usernamePassword(credentialsId: 'kesavan.athimoolam.EMAIL', passwordVariable: 'KESAVAN_EMAIL_PASS', usernameVariable: 'KESAVAN_EMAIL')]) {
           				EMAIL_FROM ='srujanareddy.katta@servicenow.com'
           				 
           				def recipients_arr = params.EMAIL_CSV.split(",")
					    for(r=0;r<recipients_arr.size();r++){
					    		if (! recipients_arr[r].endsWith('@servicenow.com')) {
					             recipients_arr[r] = recipients_arr[r] + '@servicenow.com' 
					        }
						}
						print recipients_arr
						
						def EMAIL_TO= recipients_arr.join(",")
						print EMAIL_TO 
					        
           				sh "python send_email.py '" + KESAVAN_EMAIL +  "' '" + KESAVAN_EMAIL_PASS +  "' '" + "Recent commits in " + params.BRANCH +" in the past "+ params.DAYS+" days' '"+EMAIL_TO+"'"
           			}
           			 
                }
            }
        }
       
    }
    
    
}





void write_python_script(){

def python_script_contents = '''    
import smtplib
import sys

from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
EMAIL_OF_USERNAME = sys.argv[1]
PASSWORD = sys.argv[2]
SUBJECT = sys.argv[3] 
EMAIL_TO = sys.argv[4] 

TEXT = ''
with open("changelist.html", "r") as myfile:
    TEXT=myfile.read() 
# Create message container - the correct MIME type is multipart/alternative.
msg = MIMEMultipart("alternative")
msg["Subject"] = SUBJECT 
msg["From"] = EMAIL_OF_USERNAME
msg["To"] = EMAIL_TO



emailbody = MIMEText(TEXT,"html")
msg.attach(emailbody)

mailserver = smtplib.SMTP("smtp.office365.com",587)
mailserver.ehlo()
mailserver.starttls()
mailserver.login(EMAIL_OF_USERNAME, PASSWORD)
mailserver.sendmail(EMAIL_OF_USERNAME,EMAIL_TO,msg.as_string())
mailserver.quit()
'''

writeFile file: 'send_email.py', text: python_script_contents

}
