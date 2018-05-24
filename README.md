# Setting things up:
## Deploy NagBot to a server or cloud service.
### Heroku
## Setting up NagBot in Slack
### Create a NagBot Slack app on [api.slack.com](https://api.slack.com/apps?utm_source=events&utm_campaign=build-bot-workshop&utm_medium=workshop)

To set up the NagBot app to work with Slack we need to create a Slack App for your workspace.
In your browser navigate to https://api.slack.com/apps . You will be presented with an option to create a new app.

1

Select the friendly green button and create a new app.

Give the App a name : NagBot and assign it to s Workspace.

2

To enable NagBot to interactively message users of your workspace Slack needs a url to send messages to and a url which define what actions to take. eg escalate an out of cardinality login.

Navigate to the Interactive Components in the left menu list.

3

Turn on interactivity, then fill in the **Request URL** and **Options Load URL** fields, with the URL home of your NagBot app followed by `/slack/message_options` in the request URL and `/slack/message_actions` in the Options Load URL field.

4

**Don’t forget to save changes.**

To make NagBot appear more like a standard user in your workspace you can set the Bot User details:

5

**Don’t forget to save.**

##Event Escalation
Security personnel will receive messages in the escalation channel from the bot as you have named it here.

### Tokens, Verification and Environmental variables

So that Slack will accept message from your NagBot and can send messages back to Slack we need a:
**SLACK_BOT_TOKEN** called the Bot User OAuth Token.
**SLACK_VERIFICATION_TOKEN** conveniently enough call Verification Token.
It is important that these tokens remain secret, do not share them. 
To retrieve the **SLACK_BOT_TOKEN** 
Navigate to OAuth & Permissions in the left hand side menu:

6

 Copy this to like named environmental variables on the server hosting your NagBot App.
 
`$export SLACK_BOT_TOKEN=”xoxp-secret_token”`

To retrieve the **SLACK_VERIFICATION_TOKEN**

Navigate to Basic Information in the left hand side menu:

7

 Copy this to like named environmental variables on the server hosting your NagBot App.
 
`$export SLACK_VERIFICATION_TOKEN =”another secret token”`

### Create a channel to receive escalation events

Finally we need to create a Slack channel to which suspicious login events can be sent. Only administrative personnel and NagBot need access to this channel.
Create a channel, using either Slack app or the Slack Web site, in the workspace you wish NagBot to be active click the **+** next to Channels 

8

Fill in the displayed fields:
- change Public to Private if you want escalations to remain private.
- invite the users and NagBot.
- then Create Channel.

9

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

10

Respond directly to the user if all is OK

Or 

Escalate the event to admin or security personnel via the designated escalation channel.

11
