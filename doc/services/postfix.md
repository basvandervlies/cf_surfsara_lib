# postfix

Source: [postfix.cf](/services/postfix.cf)

This bundle will check and configure postfix for Debian and CentOS. The following
files will be generated from mustache templates:
 * /etc/aliases
 * /etc/postfix/main.cf
 * /etc/postfix/master.cf
 * /etc/postfix/dynamicmaps.cf
 * /etc/postfix/recipient_canonical
 * /etc/postfix/header_checks
 * /etc/postfix/transport

Created by the bundle postfix_virtual_alias_map()
 * /etc/postfix/virtual_alias_maps.cf

If one of the files is changed then the following ''class'' will be set:
 * sara_etc_aliases
 * sara_etc_postfix_main_cf
 * sara_etc_postfix_master_cf
 * sara_etc_postfix_dynamicmaps_cf
 * sara_etc_postfix_recipient_canonical
 * sara_etc_postfix_header_checks
 * sara_etc_postfix_transport

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/postfix/
 * templates/postfix/json


## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "postfix",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/postfix/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "postfix_json_files" slist => { "zimbra.json" };
```

The variable must be ''postfix_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_postfix` class, eg:
 * `-DDEBUG_postfix`

## def.cf/json

See [default.json](/templates/postfix/json/default.json) what the default values are and
which variables can be overriden

Here are some examples how to use it:
 * specify postfix configuration in def.cf:
```
vars:
    "postfix_json_files" slist => { "zimbra.json" };
```
 * Set/Override options variable in a *def.json* file:
```json
"vars": {
    "postfix" : {
        "alias_root_email_address": "noreply@surfsara.nl",
        "json_files": [ "zimbra.json" ],
        "file_aliases_lines": [ {"name": "beowulf", "value": "|\"/usr/bin/run_email2trac --project beowulf\""} ]
    }
}
```

 * override server setting in def.cf
```
vars:
    "postfix" data => parsejson( '{ "alias_root_email_address":  "file_aliases_lines"  }' );
```


### virtual_alias_maps

with this bundle you can define youw own mustache template with json data for creating
the virtual alias map file(s). The format of the `virtual_alias_maps` is:
```json
"virtual_alias_maps": {
    "mustache_file": {
        "data": {},
        "delimiter": "",
        "dest": "",
        "protocol": ""
    }
}
```
j
An example for defining an LDAP alias map for mustache (.forward is fetch from LDAP). This will copy the template
`ldap_aliases_map.mustache` and expand it with the give json data.
```json
"postfix": {
    "classes" : {
        "VIRTUAL_MAPS": [ "mta.example.com" ],:
    },
    "virtual_alias_maps": {
        "ldap_aliases_map.mustache": {
            "delimiter": ":",
            "dest": "/etc/postfix/virtual_alias_maps.cf",
            "protocol": "ldap",
            "data": {
                "bind": true,
                "bind_options": {
                    "dn": "<your_dn>",
                    "pw": "<your_bind_password>"
                },
                "port": "636",
                "query_filter": "(uid=%s)",
                "result_attribute": "mail",
                "search_base": "ou=Users,dc=example,dc=com",
                "server": "ldaps://ldap.example.com"
            }
        }
    }
}
```
