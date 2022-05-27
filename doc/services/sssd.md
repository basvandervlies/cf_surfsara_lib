
# SSSD

This will install and configure SSSD and is tested for SUSE and RedHat based OS. This service
will create a sssd.conf based on the sssd.conf.mustache template

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`:
```
"vars": {
    "scl_services_enabled": [
            "...",
            "sssd",
            "..."
    ]
}

```

The bundle will always read the [default.json](/templates/sssd/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf



## DEBUG

If you want to debug these bundle set the `DEBUG_sssd` class, eg:
 * `-DDEBUG_sssd`

# def.cf/json

See [default.json](/templates/sssd/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify resolv configuration in def.cf:
```
 vars:
     "sssd_json_files" slist => { "cluster.json" };
```

 * override config settings in def.cf
```
  vars:
      "sssd" data => parsejson( '{ "ssh_debug_level": "9" }' );
```

 * override config settings in ''def.json'':
```
"vars": {
   "sssd": {
       "json_files": [ "snellius.json" ]
   },
}
```

  * override variable config setting in ''def.json''
```
"sssd": {
    "ssh_debug_level": "9"
}
```
