Homely
======

A collection of glue programs for Domoticz, Frity!Box, and Telegram
using MQTT.

Out of the box, with a few lines of configuration, you can get
notifications for your preferred home automation events on your
telegram account.

E.g. "Baol's back home", "Main door opened", and so on...

But this is also an enabler for more MQTT fun!

**DRAFT OF AN IDEA**
**There is nothing here yet :)**

telegram
--------

This bot will listen to homely-telegram/out and send the received
message using the configured account and userid.

    {
        "message": "Main door open"
    }

The bot will also publish the messages received to homely-telegram/in
using the same format using webhooks.

    {
        "message": "Watch TV"
    }


fritzwho
--------

This bot will poll the Fritz!BOX API and check for added/removed
connected devices.

Every time a device gets connected or disconnected it will publish a
message to homely-fritzwho/out/MacAddress/Status, e.g.

    homely-fritzwho/out/PhoneMacAddress/Connected

Useful to know witch devices are active (e.g. phones) for automatic
presence notification.

domofilter
----------

Domofilter listens to domoticz/out messages republishes them to
homely-domofilter/out/${device}/${value}

        homely-domoticz/out/4/0
        homely-domoticz/out/4/1

Makes it easier to use *wiring* and *telegram* together.

Analgously devices can be controlled sending messages to
homely-domofilter/in/${device}/${value}

wiring
------

Wiring will listen on multiple MQTT queues and republish the messages
into other queues, after filtering and applying a transformation.

    source-queue dest-queue json

Examples of what you may find here one day
------------------------------------------

    raspberry$ domofilter
    raspberry$ telegram --telegram-key 12345678:XXXXXXXX --default-user-id 123456
    raspberry$ cat >wiring.rc <<EOF
        homely-domoticz/4/0 homely-telegram/out/ {'The main door has been closed'}
        homely-domoticz/4/1 homely-telegram/out/ {'The main door has been opened'}
        homely-domoticz/000000000000/connected homely-telegram/out/ {'Baol's home again'}
        homely-domoticz/000000000000/disconnected homely-telegram/out/ {'Baol's phone left home'}
    EOF
    raspberry$ wiring -c wiring.rc