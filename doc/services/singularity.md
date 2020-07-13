# Singularity

Source: [singularity.cf](/services/singularity.cf)

This bundle install singularity software on a node.

This bundle will generate this file from the mustache template:
 * /etc/singularity/singularity.conf

f one of the files is changed then the followong ''class'' will be set:
 * sara_etc_singularity_singularity._conf

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/singularity/
 * templates/singularity/json

## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "singularity",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/singularity/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "singularity_json_files" slist => { "disable_overlay.json" };
```

The variable must be ''singularity_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_singularity` class, eg:
 * `-DDEBUG_singularity`

## def.cf/json

See [default.json](/templates/singularity/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify singularity configuration in def.cf:
```
vars:
    "singularity_json_files" slist => { "disable_overlay.json" };
```

 * Set/Override variables in *def.json*:
```json
"singularity" : {
    "json_files": [ "test_server.json" ],
    "enable_overlay": "no"
}
```

 * override setting in def.cf
```
vars:
    "singularity" data => parsejson( '{ "enable_overlay":  "no"  }' );
```
