# tripwire

Source: [tripwire.cf](/services/tripwire.cf)

This bundle checks the given file(s) if they have changed (md5). It will report the changes
and update the database with the new checksum value.

No template file are generated, we only need josn file(s) as input. The location of this
file(s) are:
 * templates/tripwire/json

## Usage

The bundle can be run via:
 *  `"" usebundle => tripwire_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "tripwire",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/tripwire/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "tripwire_json_files" slist => { "tomcat.json" };
```

The variable must be ''tripwire_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug this bundle set the `DEBUG_tripwire` class, eg:
 * `-DDEBUG_tripwire`

## def.cf/json

See [default.json](/templates/tripwire/json/default.json) what the default values are and
which variables can be overriden.

Here is an example to check also the apache binary (def.cf):
```
vars:
    "tripwire_json_files" slist => { "apache.json" ];
```

Same can also be set in `def.json`
```json
"vars": {
    "tripwire": {
        "json_files": [ "apache.json" ]
    }
}
```

Added some extra binaries to the *standard* ones:
```json
{
"apache":  "/usr/sbin/httpd",
"sshd": "/usr/sbin/sshd"
}
```
