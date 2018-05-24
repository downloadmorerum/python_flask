# Setting things up:
## Deploy NagBot to a server or cloud service.
### Heroku

### Create a NagBot Slack app on [api.slack.com](https://api.slack.com/apps?utm_source=events&utm_campaign=build-bot-workshop&utm_medium=workshop)

To set up the NagBot app to work with Slack we need to create a Slack App for your workspace.
In your browser navigate to https://api.slack.com/apps . You will be presented with an option to create a new app.

![1_create_new_app](https://user-images.githubusercontent.com/37161577/40457232-c54952f4-5f38-11e8-8965-7e957b3be1d0.png)

Select the friendly green button and create a new app.

Give the App a name : NagBot and assign it to s Workspace.

![2_app_name_workspace](https://user-images.githubusercontent.com/37161577/40457832-0c58edc8-5f3c-11e8-9cc9-8804181748bb.png)

To enable NagBot to interactively message user of your workspace Slack needs a url to send messages to and a url which define what actions to take. eg escalate an out of cardinality login.
Navigate to the Interactive Components in the left menu list.

![3_menu_list](https://user-images.githubusercontent.com/37161577/40457873-46d0dcfe-5f3c-11e8-9e57-8f71139d40ed.png)

Turn on interactivity, then fill in the request URL and Options Load URL fields, with the URL home of your NagBot app followed by /slack/message_optoins in the request URL and /slack/message_actions in the Options Load URL field.

![4_turnon_interactivity](https://user-images.githubusercontent.com/37161577/40457895-5d1464a4-5f3c-11e8-8eca-5fb5711d572c.png)

**Don’t forget to save changes.**
To make NagBot appear more like a standard user in your workspace you can set the Bot User details:

![5_botuser](https://user-images.githubusercontent.com/37161577/40457913-730dfafe-5f3c-11e8-92d2-8457b6362bc3.png)

**Don’t forget to save.**
Security personnel will receive messages in the escalation channel from the bot as you have named it here.

### Tokens, Verification and Environmental variables

So that Slack will accept message from your NagBot and can send messages back to Slack we need a:
**SLACK_BOT_TOKEN** called the Bot User OAuth Token.
**SLACK_VERIFICATION_TOKEN** conveniently enough call Verification Token.
It is important that these tokens remain secret, do not share them. 
To retrieve the **SLACK_BOT_TOKEN** 
Navigate to OAuth & Permissions in the left hand side menu:

![6_ oauth_token](https://user-images.githubusercontent.com/37161577/40457930-87768e84-5f3c-11e8-8aed-202c10e4ff4c.png)

 Copy this to like named environmental variables on the server hosting your NagBot App.
`$export SLACK_BOT_TOKEN=”xoxp-secret_token”`

To retrieve the **SLACK_VERIFICATION_TOKEN**
Navigate to Basic Information in the left hand side menu:

![7_verification_token](https://user-images.githubusercontent.com/37161577/40457951-a346e7f8-5f3c-11e8-9821-383fbbc7d1f5.png)

 Copy this to like named environmental variables on the server hosting your NagBot App.
`$export SLACK_VERIFICATION_TOKEN =”another secret token”`

### Create a channel to receive escalation events

Finally we need to create a Slack channel to which suspicious login events can be sent. Only administrative personnel and NagBot need access to this channel.
Create a channel, using either Slack app or the Slack Web site, in the workspace you wish NagBot to be active click the **+** next to Channels 

![8_addchannel](https://user-images.githubusercontent.com/37161577/40457970-bb8abbaa-5f3c-11e8-816f-138b9679a543.png)

Fill in the displayed fields:
- change Public to Private if you want escalations to remain private.
- invite the users and NagBot.
- then Create Channel.

![9_escalatechannel](https://user-images.githubusercontent.com/37161577/40457980-cdecc1c6-5f3c-11e8-8777-7fc93067918a.png)

Additional users can always be added to the channel later.

NagBot needs to know where to send the escalation message this is identified by the channel id. 
The easiest way to find it is in the `url` field of your browser. Navigate to the slack channel and the channel id will appear after `/message/####`
For example:
`https://yourserver.com/messages/ABCDEFGHI/`

Copy this to a NAGBOT_SLACK_CHANNEL environmental variable onto your server.
`$export NAGBOT_SLACK_CHANNEL=”ABCDEFGHI”`

### Test it works.

To test it generate an alert from Elastalert or use curl to send Nagbot a json test event.
For example:

## test_event.json
`{
 "system": {
  "auth": {
   "hostname": "yourhost",
   "ssh": {
    "geoip": {
     "continent_name": "Oceania",
     "city_name": "Pakenham East",
     "country_iso_code": "AU",
     "region_name": "Victoria",
     "location": {
      "lon": 145.4741,
      "lat": -38.0702
     }
    },
    "method": "password",
    "port": "54084",
    "ip": "8.4.4.4",
    "event": "Accepted"
   },
   "pid": "8569",
   "user": "your user id",
   "timestamp": "May 18 02:34:24"
  }
 },
 "offset": 162887,
 "beat": {
  "hostname": "your elastic serach server",
  "name": " your elastic serach server ",
  "version": "6.2.3"
 },
 "prospector": {
  "type": "log"
 },
 "source": "/var/log/auth.log",
 "fileset": {
  "module": "system",
  "name": "auth"
 }
}`
From the command line use curl to send the json to NagBot
## Curl command:
`curl -XPOST --header "Content-Type: application/json" 'https://yourserver.nagbotapp.com/api/json/nagbot/' -d @test_event.json`


### Good to go
That’s it NagBot should be ready to use.
NagBot will send messages directly to a user:

![10_nagbotmessage](https://user-images.githubusercontent.com/37161577/40458021-f5b0040c-5f3c-11e8-8a16-8db93458c128.png)

Respond directly to the user if all is OK

Or 

Escalate the event to admin or security personnel via the designated escalation channel.

![11_securityalert](https://user-images.githubusercontent.com/37161577/40458033-07104de2-5f3d-11e8-84d3-52e3558a1c60.png)
