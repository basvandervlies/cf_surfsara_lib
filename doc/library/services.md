# services.cf

Source: [services.cf](/masterfiles/lib/scl/services.cf)

You can control which services must be run by specifying the following variable in:
 * def.json
```json
"scl_services_enabled": [ "ssh", "ntp" ]
```

 * def.cf
```
"scl_services_enabled" slist => [ "ssh", "ntp" ];
```

This variable will include the cfengine services file from the directory
`def.scl_services_dir` (default value: scl/services) and run all bundles that have
the meta tag, eg: `template_ssh`. The bundle run can be protected by an class
statement, default is `any`, eg:
```json
"ssh": {
    "run_class": "debian|centos"
}
```
This will run the ssh service only for debian and centos hosts.

When `def.scl_services_enabled` is defined it will execute these bundles:
 1. first read merge all json data for all defined services (`scl_services_data`)
 1. expand the CFengine variables for all service bundle data (`scl_services_data_expand`)
 1. run the service  with the specified json data `(scl_services_run`)
