# NTP

Source: [ntp.cf](/services/ntp.cf)

This bundle will generate these files from mustache templates:
 * /etc/ntp.conf
 * /etc/default/ntpdate (debian), /etc/sysconfig/ntpdate (centos)
 * /etc/default/ntp (debian), /etc/sysconfig/ntpd (centos)

if one of the files is changed then the following ''class'' will be set:
 * scl_etc_ntp_conf
 * scl_etc_default_ntp
 * scl_etc_default_ntpdate

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/ntp/
 * templates/ntp/json

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```
"vars": {
    "scl_services_enabled": [
            "...",
            "ntp",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/cron/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "ntp_json_files" slist => { "debian.json" };
```

The variable must be ''ntp_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_ntp` class, eg:
 * `-DDEBUG_ntp

## def.cf/json

See [default.json](/templates/ntp/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify ntp configuration in def.cf:
```
vars:
    "ntp_json_files" slist => { "debian.json" };
```
 * Set/Override the daemon options variable in ''def.json'':
```
"ntp" : {
    "json_files": [ "debian.json" ],
    "daemon_options": "-g"
}
```
 * override server setting in def.cf
```
vars:
    "ntp" data => parsejson( '{ "server" : [ "ntp_test.surfsara.nl" ] }' );
```
