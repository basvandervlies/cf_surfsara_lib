# yum

Source: [yum.cf](/services/yum.cf)

This bundle will configure the:
 * yum.conf -> sara_yum_conf class will set
 * repo files in /etc/yum.repos.d

These will be generated with the aid of the mustache templates with json data.
The templates/json files are located in:
 * templates/yum/
 * templates/yum/json
 * templates/yum_repository/
 * templates/yum_repository/json

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "yum",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/yum/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "yum_repository_json_files" slist => { "surfsara.json" };
```

The variable must be `yum_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_yum` class, eg:
 * `-DDEBUG_yum`

## def.cf/json

See what the default values are and which variables can be overriden:j
 * [yum default.json](/templates/yum/json/default.json)
 * [yum repository default.json](/templates/yum_repository/json/default.json)


With yum you can specify 2 different type of lists for external json data:
 1. yum_json_files --> yum.conf
 1. yum_repository_json_files --> yum repo files

Here are some examples how to use it.

add an extra repo:
 * def.cf
```
any::
    "yum_repository_files" slist => { "epel.json" };
```

 * def.json
```json
"vars": {
    "yum_repository_files": [ "epel.json" ],
}
```

override yum conf setting:
 * def.cf
```
vars:
    "yum" data => parsejson( '{ "assumeyes":  "0"  }' );
```

 * def.json
```json
"vars": {
    "yum": {
        "assumeyes":  "0"
    }
}
```
