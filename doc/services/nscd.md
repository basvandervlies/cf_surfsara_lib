# nscd

Source: [nscd.cf](/services/nscd.cf)

This bundle will generate these files from mustache templates:
 * /etc/nscd.conf

f one of the files is changed then the followong ''class'' will be set:
 * sara_etc_nscd_conf

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/nscd/
 * templates/nscd/json

## Usage

The bundle can be run via:
 *  `"" usebundle => nscd_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "nscd",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/nscd/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "nscd_json_files" slist => { "disable_cache.json" };
```

The variable must be `nscd_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_nscd` class, eg:
 * `-DDEBUG_nscd`


## def.cf/json

See [default.json](/templates/nscd/json/default.json) what the default values are and
which variables can be overriden

The following must be set in the specific `def.json` hostfile
```json
"vars": {
    "nscd": {
        ...
        "json_files": [ "disable_cache.json" ],
        ...
    }
}
```

Here are some examples how to use it:
 * specify nscd configuration in def.cf:
```
vars:
    "nscd_json_files" slist => { "disable_cache.json" };
```
 * Set/Override the daemon options variable in ''def.json'':
```json
"nscd" : {
    "debug-level": "1"
}
```
 * override server setting in def.cf
```
vars:
    "nscd" data => parsejson( '{ "max-threads" :  "7" }' );
```
