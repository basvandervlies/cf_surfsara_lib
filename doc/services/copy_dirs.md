# COPY_DIRS

Source: [copy_dirs.cf](/services/copy_dirs.cf)

This will copy directories from policy hub to the host, with the aid of library
function [scl_service_copy_dirs}(/masterfiles/lib/scl/files.cf).

## Usage

The bundle can be run via:
 * `def.scl_services_enabled` (prefered)
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "copy_dirs",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/copy_dirs/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "copy_dirs_json_files" slist => { "lisa.json" };
```

The variable must be `copy_dirs_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_copy_dirs` class, eg:
 * `-DDEBUG_copy_dirs`

## def.cf/json

See [default.json](/templates/copy_dirs/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify copy_dirs configuration in def.cf:
```
vars:
    "copy_dirs_json_files" slist => { "lisa.json" };
```
 * Use `def.json`:
```
    "copy_dirs" : [
        "copy_dirs": {
            "dest": "/usr/sara",
            "source": "data/copy_dirs/usr_sara",
            "purge": "true",
            "preserve": "true",
        }
    ],
