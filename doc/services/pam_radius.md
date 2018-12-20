# pam_radius

Source: [pam_radius.cf](/services/pam_radius.cf)

PAM Radius Module allows any PAM-capable machine to become a RADIUS client for authentication and accounting requests. The actual
authentication will be performed by a RADIUS server.

This bundle install the software on a node and generete the file from mustache templates:
 * /etc/pam_radius_auth.conf (debian)
 * /etc/pam_radius.conf (centos)
 * cfengine variable : `$(pam_radius.config_file)`

When the configuration flle has been changed the following class will be set:
 * `canonify("sara$(pam_radius.config_file)")`

These files wille be generated  with the aid of mustache templates with json data.
The templates are located in:
 * templates/pam_radius
 * templates/pam_radius/json

## Usage

The bundle can be run via:
 *  `"" usebundle => pam_radius_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "pam_radius",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/pam_radius/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "pam_radius_json_files" slist => { "surfsara.json" };
```

The variable must be ''pam_radius_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_pam_radius` class, eg:
 * `-DDEBUG_pam_radius`

## def.cf/json

See [default.json](/templates/pam_radius/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify pam_radius configuration in def.cf:
```
vars:
    "pam_radius_json_files" slist => { "policy_server.json" };
```
 * Set/Override the servers option in *def.json*:
```json
"pam_radius" : {
    "servers": [
        {
            "server" : "192.168.0.1",
            "secret" : "geheim",
            "timeout" : "5"
        }
    ]
}
```
 * override servers setting in def.cf
```
vars:
    "pam_radius" data => parsejson( '{ "servers": [{ "server" : "192.168.0.1", "secret" : "geheim", "timeout" : "5" }]  }' );
```
