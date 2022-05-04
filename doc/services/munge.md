
# Munge

Source: [munge.cf](/services/munge.cf)

This bundle copies a munge key to a node.
 * `/etc/munge/munge.key`

This bundle will install munge and configure the following files with mustache templates:
 * /etc/default/munge (debian)

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/scl/munge/
 * templates/scl/munge/json

This bundle also makes sure that the proper owner (munge:munge) and permissions (400) are set.

The following json variables can be set in def.cf/json to invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/scl/files.cf)

## Usage

The bundle can be run via:
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "munge",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/munge/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "munge_json_files" slist => { "lisa.json" }
```

The variable must be `cron_json_files` and with this setup 1 extra json file will be  merged.

### Debug

To debug this bundle set the `DEBUG_munge` class:

- `-DDEBUG_munge`

## def.cf/json

See [default.json](/templates/munge/json/default.json) what the default values are and
which variables can be overriden.


### copy_files

With this variable is set it will copy the specified file (usually the munge key) to the
specified destination, example:
```json
"copy_files": [
    {
        "dest": "$(munge.munge_key_file)",
        "source": "data/munge/lisa/munge.key",
        "mog": [ "0400", "munge", "munge" ],
        "run_bundle": "munge_daemons_restart"
    }
]
```
Where `data` is a **cf-serverd shortcut**.
