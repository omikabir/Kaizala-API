# Kaizala-API
## Python Snipet to send message and read Job status from group using Kaizala API

### Basic Requirements and Operations:

##### To Send SMS, the process executes in 2 Steps, In First Steps, Needs to generate an access token. To generate, 4 items required as following.
##### These items can be generated from https://manage.kaiza.la/
	1. Connector
	2. Connector Token [Belong to connector]
	3. applicationId [Belong to connector]
	4. applicationSecret [Belong to connector]

#### While we will have AccessToken, We can send SMS using following 3 object
	     1. EndPoinUrl
	     2. Token (generated by using connector token)
	     3. groupId [what can be fetched from, https://manage.kaiza.la/Group/Conversation/groupId

###### Finally, Calling following link, will send msg to group
      URL ["https://{endpoint-url}/v1/groups/{group-id}/messages"]

##### PythonSnip:: Modify all veriable as yours

```
#Ref:https://docs.microsoft.com/en-us/rest/kaizala/

import requests
import json

class Kaizala:
    def __init__(self,appId,appSecret,connToken):
        url_AccToken = "https://api.kaiza.la/v1/accessToken"
        self.AppId = appId
        self.AppSecret = appSecret
        self.ConnToken = connToken
        body1 = ""
        headers = {
            'applicationId': self.AppId,
            'applicationSecret': self.AppSecret,
            'refreshToken': self.ConnToken
            }
        self.response = requests.request("GET", url_AccToken, data=body1, headers=headers)
        print(self.response.text)
        Jsn1 = json.loads(self.response.text)
        self.Tokn = Jsn1["accessToken"]
        self.endUrl = Jsn1["endpointUrl"]
    def SendSms(self,groupID,text):
        Url_SendSMS = self.endUrl + "v1/groups/"+ groupID +"/messages"
        hd = {
            'accessToken': self.Tokn
        }
        bdy = {
            "message": text,
            "sendToAllSubscribers" : "True"
        }
        resp = requests.request("POST", Url_SendSMS, data=bdy, headers=hd)
    def FetchAction(self,groupID,ActionType):
        #GET https://{endpoint-url}/v1/groups/{test-group-id}/actions?actionType={actionType}
        Url_SendSMS = self.endUrl + "v1/groups/"+ groupID +"/actions?actionType=" + ActionType
        hd = {
            'accessToken': self.Tokn
        }
        bdy = ""
        try:
            resp = requests.request("GET", Url_SendSMS, data=bdy, headers=hd)
            return resp
        except:
            print('ActionType not found, Please check')

## just needs to change following credential and pass your variable
applicationId = "yyaaaa-yyyy-yyyy-xxx-xxxxx"        
ConnectorToken = "NvZnQ6d2luZG93cy1henVyZTp6dW1vIn0.QDEFj7xdNaITNC3MUmsTpzsrc649J6UipWzcm-KsRUMxxxxxx"
applicationSecret = "0W4XXXXAAA"
GroupID = "xaa42edfd-fswa-4rd3-aexx-xxxx111aaaa"
x = Kaizala(applicationId,applicationSecret,ConnectorToken)
## To SEnd SMS
x.SendSms(GroupID,"Test SMS")
## To read status of an Action
getjob = x.FetchAction(GroupID,"cc")
js = json.loads(getjob.text)
print(js)
```
