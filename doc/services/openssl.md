# OPENSSL

Source: [openssl.cf](/services/openssl.cf)

This bundle will generate this file from mustache template:
 * /etc/ssl/openssl.cnf


These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/openssl/
 * templates/openssl/json

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "openssl",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/openssl/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "openssl_json_files" slist => { "liza.json" };
```

The variable must be ''openssl_json_files'' and with this setup 1 extra json file will be merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_openssl` class, eg:
 * `-DDEBUG_openssl`

##  def.cf/json

See [default.json](/templates/openssl/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify openssl configuration in def.cf:
```
vars:
    "openssl_json_files" slist => { "liza.json" };
```
