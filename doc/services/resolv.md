
# RESOLV

Source: [resolv.cf](/services/resolv.cf)

This bundle will generate this file from mustache templates:
 * /etc/resolv.conf

If one of the files is changed then the following ""class"" will be set:
 * scl_etc_resolv_conf

These templates are located in:
 * templates/resolv
 * templates/resolv/json

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```
"vars": {
    "scl_services_enabled": [
            "...",
            "resolv",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/resolv/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "resolv_json_files" slist => { "cua.json" };
```

The variable must be ""resolv_json_files"" and with this setup 1 extra json file will be merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_resolv` class, eg:
 * `-DDEBUG_resolv`

## def.cf/json

See [default.json](/templates/resolv/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify resolv configuration in def.cf:
```
vars:
    "resolv_json_files" slist => { "cua.json" };
```

 * override config settings in def.cf
```
vars:
    "resolv" data => parsejson( '"options":[ "ndots:2" ]' );
```

 * override config settings in *\<filename\>.json*:
```
"vars": {
    "resolv": {
        "json_files": [ "search_mail.json" ]
    }
}
```

 * override variable config setting in  *\<filename\>.json*:
```
"resolv": {
    "search": [
        { "domain": "ia.surfsara.nl" }
    ]
}
```
