
# ENROOT

Source: [enroot.cf](/services/enroot.cf)

This bundle will generate this file from mustache template:
 * /etc/enroot/enroot.conf

## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "enroot",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/enroot/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "enroot_json_files" slist => { "lisa.json" };
```

The variable must be ''enroot_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_enroot` class, eg:
 * `-DDEBUG_enroot`

##  def.cf/json

See [default.json](/templates/enroot/json/default.json) what the default values are and
which variables can be overridden.

Here are some examples how to use it:
 * specify enroot configuration in def.cf:
```
vars:
    "enroot_json_files" slist => { "lisa.json" };
```

 * Set/Override variables in *def.json*:
```json
"enroot": {
    "classes": {
        "SERVER" : [ "software1" ]
    },
    "json_files": [ "lisa.json" ]
}
```
