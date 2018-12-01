# opsgenie-monit

The scripts in this repository are wrappers for the OpsGenie API which will
open and close OpsGenie incidents from the command line. These scripts are
designed to help you integrate monit with OpsGenie.

This is based off of https://github.com/pinterest/pagerduty-monit

## Usage ##

`opsgenie-trigger <service-name>`

### Example ###

`opsgenie-trigger nginx` on the server "webserver1.example.com"
will trigger a OpsGenie incident with the subject "nginx failed on
webserver1".

## Prerequisites ##
Requires opsgenie library for Python. You can run `pip install opsgenie`.
See https://docs.opsgenie.com/docs/opsgenie-python-api for more information.

## To configure OpsGenie ##

To configure OpsGenie:

* login as an administrator
* Go to teams https://app.opsgenie.com/teams
* Select your team
* Select Integrations Tag
* Add Integration
* Select API
* Copy the API Key
* Replace the `YOUR_API_KEY` in `API_KEY = "YOUR_API_KEY"` with your key in both scripts

Other Configuration parameters:

* `REALERT_AFTER_4_HOURS`: By default, each alert will only be fired once. This allows the
alert to re-fire after 4 hours.

## To configure Monit ##

To configure Monit to trigger an event with this script, save the script in a location
like /etc/monit, make sure it's executable, and use an action like this in your monitrc file:

    exec "/etc/monit/opsgenie-trigger <service-name>"

Example monit stanza to trigger an incident if nginx is not present:

    check process nginx with pidfile /var/run/nginx.pid
        if does not exist for 3 cycles
            then exec "/etc/monit/opsgenie-trigger nginx"
        else if passed for 3 cycles
            then exec "/etc/monit/opsgenie-resolve nginx"


## Other Uses ##

While the script is written for monit specifically, we use it for a variety of alerts, especially for repeated triggers.  The use is:

```
    /etc/monit/opsgenie-trigger <summary> <description>
```