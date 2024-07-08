# Zabbix alerts via Baleh

*Replace `my_zabbix_bot` and `My Zabbix Bot` below with your own names.*

## Create and test baleh bot

Add `@BotFather` contact in Baleh and press 'start',  
then type:

    /newbot
    My Zabbix Bot
    my_zabbix_bot

The bot will reply you with a token, which we will refer to as `${TOKEN}` below.

Add a `@my_zabbix_bot` contact to your Baleh, press 'start' and send a random message.

Now we need to retrieve a `chat_id` for this conversation.

    $ curl "https://tapi.bale.ai/bot${TOKEN}/getUpdates"
    {"ok":true,"result":[{"update_id":...,"message":{"message_id":...,"from":{...},"chat":{"id":<CHAT_ID>,...},"date":...,"text":...}}]}
    
The `<CHAT_ID>` above is what you need. It's the `RESULT.result[0].message.chat.id` key in JSON notation.

Test that it actually works:

    $ curl -X POST --retry 5 --retry-delay 0 --retry-max-time 60 --data-urlencode "chat_id=${CHAT_ID}" --data-urlencode "text=Hello" "https://tapi.bale.ai/bot${TOKEN}/sendMessage?disable_web_page_preview=true"

## Install zabbix media type

Find out your `AlertScriptsPath` from the `zabbix_server.conf`. It's `${datadir}/zabbix/alertscripts` by default.

Put the `Baleh.sh` file (attached) to that folder. Don't forget to `chmod +x telegram.sh`

Open your Zabbix web interface, navigate to 'Administration - Media types - Create',  
Put in the form:
* Name: `Baleh`
* Type: `Script`
* Script name: `Baleh.sh`
* Script parameters (Zabbix 3 only):
    - `{ALERT.SENDTO}`
    - `{ALERT.SUBJECT}`
    - `{ALERT.MESSAGE}`
    
## Action

Ensure that you have an action (Configuration - Actions) which sends to the Baleh media type.

## User

Navigate to 'Administration - Users - `<Your user>` - Media', add Baleh, in the 'Send to' field put your `${CHAT_ID}` and `${TOKEN}`, separated with space, for example: `12345678 123456789:AAaaaaAAAaaa_aAAaaAaA-aaAAa`.


## Refs:
* https://docs.bale.ai/
