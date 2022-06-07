# NHC

Source: [nhc.cf](/services/nhc.cf)

The service file installs nhc software on a node and generates the following files:
 * /etc/nhc/nhc.conf
 * /etc/default/nhc (Debian)
 * /etc/sysconfig/nhc (centos/redhat)

If one of the files is changed then the following ''class'' will be set:
 * scl_etc_nhc_conf
 * scl_etc_default_nhc_conf or scl_etc_sysconfig_nhc

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/nhc/
 * templates/nhc/json

The following json variables can be set in def.cf/json to invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/scl/files.cf)
 * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```
"vars": {
    "scl_services_enabled": [
            "...",
            "nhc",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/nhc/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "nhc_json_files" slist => { "lisa.json" };
```

The variable must be `lisa_json_files` and with this setup 1 extra json file will be merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_nhc` class, eg:
 * `-DDEBUG_nhc`


## def.cf/json

See [default.json](/templates/nhc/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify nhc configuration in def.cf:
```
vars:
    "nhc_json_files" slist => { "lisa.json" };
```
 * Copy extra files and override debug:
```
    "nhc" : {
        "copy_files": {
            "dest": "$(nhc.scripts_dir)/sara_health_checks.nhc",
            "src": "data/nhc/scripts/sara_health_checks.nhc",
            "mode": "0644", "owner": "root", "group": "root"
        },
        "debug": "1",
    },
```

 * override server setting in def.json
```
"vars": {
    "nhc": {
         "debug":  "1"
   }
}
```
