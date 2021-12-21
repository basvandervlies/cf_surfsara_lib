
# node_exporter

Source: [node_exporter.cf](/services/node_exporter.cf)

This bundle will generate these/this file(s) from mustache templates:

 * /etc/default/node_exporter
 * /etc/systemd/system/node_exporter.service (systemd system)
 * /etc/init.d/node_exporter (non systemd system)

If one of the files is changed then the following ""class"" will be set:
 * sara_etc_default_node_exporter
 * sara_etc_systemd_system_node_exporter_service
 * sara_etc_init_d_node_exporter

These templates are located in:
 * templates/node_exporter
 * templates/node_exporter/json

The following json variables can be set in def.cf/json to invoke files bundles:
  * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "node_exporter",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/node_exporter/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "node_exporter_json_files" slist => { "sara.json" };
```

The variable must be `node_exporter_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_node_exporter` class, eg:
 * `DDEBUG_node_exporter`

## def.cf/json

See [default.json](/templates/node_exporter/json/default.json) what the default values are and
which variables can be overriden.


### copy_dirs

When this variabele is set it will copy the directoy to the specified destination and can run a bundle
if there are changes, eg:
```json
"copy_dirs": [
    {
        "dest": "$(scl.node_exporter[dir])",
        "exclude_dirs": [ ".git", ".svn" ],
        "purge": "true",
        "run_bundle": "node_exporter_restart",
        "source": "cf_bundles_dir/prometheus_exporters/node_exporter-0.15.2"
    }
]
```

=== cron_jon ===

When this variabele is set it will create a cron file in  `/etc/cron.d` with name `sys2prometheus`, eg:
```
#!json
{
    "cron_job": {
        "minute": "*/4",
        "hour": "*",
        "day_of_month": "*",
        "month": "*",
        "day_of_week": "*",
        "user": "root",
        "command": "/usr/sara/sbin/sys2prometheus 2>/dev/null"
    }
}
```
