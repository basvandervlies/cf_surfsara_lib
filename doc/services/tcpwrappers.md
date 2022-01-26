# tcpwrappers

Source: [tcpwrappers.cf](/services/tcpwrappers.cf)

This bundle will generate these files from mustache templates:
 * /etc/hosts.allow
 * /etc/hosts.deny

If one of the files is changed then the followong *class* will be set:
 *  scl_tcpwrappers_etc_hosts_allow
 *  scl_tcpwrappers_etc_hosts_deny

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/tcpwrappers/
 * templates/tcpwrappers/json


## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "tcpwrappers",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/tcpwrappers/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "tcpwrappers_json_files" slist => {
            "allow_ssh.json" ,
            "allow_http.json" ,
        };
```

The variable must be `tcpwrappers_json_files` and with this setup 2 extra json files are being merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_tcpwrappers` class, eg:
 * `-DDEBUG_tcpwrappers

## def.cf/json

See [default.json](/templates/tcpwrappers/json/default.json) what the default values are and
which variables can be overriden

Here are some examples how to use it
 * specify tcpwrappers configuration in def.cf:
```
vars:
    "tcpwrappers_json_files" slist => { "allow_ssh.json", "allow_http.json" };
```

 * specify tcpwrappers configuration in def.json:
```json
"vars": {
    "tcpwrappers": {
        "json_files": [ "allow_ssh.json", "allow_http.json" ]
    }
}
```

 * override allow_ssh setting in def.cf:
```
vars:
    "tcpwrappers" data => parsejson( '{ "allow_ssh" : [ {"allow": ... } ] }');
```

 * override allow_ssh setting in def.json:
```
"tcpwrappers" : {
    "json_files" : [ "allow_http.json" ],
    "allow_ssh": [
        {
            "allow": "10.101.32.0/255.255.255.0",
            "desc": "ALLOW admin lan"
        }
    ]
}
```
