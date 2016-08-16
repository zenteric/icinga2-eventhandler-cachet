# icinga2-eventhandler-cachet
A icinga2 event handler to push icinga2 notifications to Cachet API

## Prerequisites
 - Have a working Cachet installation (last tested cachet 2.2)
 - Have created some Cachet components to interact with
 - Have an icinga2 instance with PHP CLI and php-curl installed

## Installation

 - Get a Cachet API key: Create a new user in Cachet dashboard, login with this user, and get the API key in his profile.
 - Copy cachet_notify to /etc/icinga2/scripts (depending on your configuration)
 - Change URL and API key in cachet_notify source code
 - Try it: `./cachet_notify 'My Cachet component' 'My icinga2 service' CRITICAL HARD 'The service is Critical'`

## Configuration

 - Make a icinga2 command:
```
object NotificationCommand "cachet-incident-notification" {
    import "plugin-notification-command"

    command = [ SysconfDir + "/icinga2/scripts/cachet-incident-notification" ]

    env = {
      NOTIFICATIONTYPE = "$notification.type$"
      SERVICEDESC = "$service.name$"
      HOSTALIAS = "$host.display_name$"
      HOSTADDRESS = "$address$"
      SERVICESTATE = "$service.state$"
      LONGDATETIME = "$icinga.long_date_time$"
      SERVICEOUTPUT = "$service.output$"
      NOTIFICATIONAUTHORNAME = "$notification.author$"
      NOTIFICATIONCOMMENT = "$notification.comment$"
      HOSTDISPLAYNAME = "$host.display_name$"
      SERVICEDISPLAYNAME = "$service.display_name$"
      USEREMAIL = "$user.email$"
    }
}

```
 - Add a script on your scripts folder (cachet-incident-notification):
```
#!/bin/sh

# Icinga2 variables (can also be defined on config)

#Notification Type:$NOTIFICATIONTYPE
#Service: $SERVICEDESC
#Host: $HOSTALIAS (Matching component name in cachet)
#Address: $HOSTADDRESS
#State: $SERVICESTATE
#Date/Time: $LONGDATETIME
#Additional Info: $SERVICEOUTPUT
#Comment: [$NOTIFICATIONAUTHORNAME] $NOTIFICATIONCOMMENT

/etc/icinga2/scripts/cachet_notify '$HOSTALIAS' '$SERVICEDESC' '$SERVICESTATE' '$NOTIFICATIONTYPE' '$SERVICEOUTPUT'

```
 - Reload icinga2 configuration
