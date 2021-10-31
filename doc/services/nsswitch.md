
# nsswitch

Source: [nsswitch.cf](/services/nsswitch.cf)

This bundle generates `/etc/nsswitch.conf`.

this bundle will generate this file from mustache templates:
 * /etc/nsswitch.conf

If this file is changed then the following ''class'' will be set:
 * sara_etc_nsswitch_conf

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/nsswitch/
 * templates/nsswitch/json

The following json variables can be set in def.cf/json to invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/scl/files.cf)

## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "nsswitch",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/nsswitch/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "nsswitch_json_files" slist => { "lisa.json" };
```

The variable must be `nsswitch_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_nsswitch` class, eg:
 * `DDEBUG_nsswitch`

## def.cf/json

See [default.json](/templates/nsswitch/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify nsswitch configuration in def.cf:
```
vars:
    "nsswitch_json_files" slist => { "mona.json" };
```
 * specify nsswitch configuration in def.json:
```jsaon
"vars": {
    "nsswitch": {
        "group": "files"
    }
}
```
