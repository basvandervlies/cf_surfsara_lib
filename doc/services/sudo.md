# sudo

Source: [sudo.cf](/services/sudo.cf)

This bundle will generate this file from mustache template:
 * /etc/sudoers.d/surfsara

f one of the files is changed then the followong *class* will be set:
 * sara_etc_sudoers_d_surfsara

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/sudo/
 * templates/sudo/json

The default is to only allow one config file in `/etc/sudoers.d`:
 * surfsara

You can override it by disabling class `ONLY_ONE_CONFIG_FILE` in the
sudo json data secton, eg:
 * def.json
```json
"sudo": {
    "classes": {
        "ONLY_ONE_CONFIG_FILE": [ "unset" ]
    }
}
```
## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "sudo",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/sudo/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "sudo_json_files" slist => { "lisa.json" };
```

The variable must be ''sudo_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_sudo` class, eg:
 * `-DDEBUG_sudo`

##  def.cf/json

See [default.json](/templates/sudo/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify sudo configuration in def.cf:
```
vars:
    "sudo_json_files" slist => { "lisa.json" };
```

 * Set/Override variables in *def.json*:
```json
"sudo": {
    "USERHOST" : "r25n3, r25n4, batch1, login*, software*",
    "SOFTWARE" : "software*",
    "FILESERVER" : "fs*",
    "json_files": [ "lisa.json" ]
}
```

 * override variable in *def.cf*:
```
vars:
    "sudo" data => parsejson( '{ "USERHOST":  "r25n3, r25n4, batch1, login*, software*"  }' );

```
