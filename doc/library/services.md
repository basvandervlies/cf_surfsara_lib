# services.cf

Source: [services.cf](/masterfiles/lib/surfsara/services.cf)

You can control which services must be run by specifying the following variable in:
 * def.json
```json
"sara_services_enabled": [ "ssh", "ntp" ]
```

 * def.cf
```
"sara_services_enabled" slist => [ "ssh", "ntp" ];
```

This variable will include the cfengine services file from the directory
`def.sara_services_dir` (default value: surfsara/services) and run all bundles that have
the meta tag, eg: `template_ssh`. The bundle run can be protected by an class
statement, default is `any`, eg:
```json
"ssh": {
    "run_class": "debian|centos"
}
```
This will run the ssh service only for debian and centos hosts.

When `def.sara_services_enabled` is defined it will execute these bundles:
 1. first read merge all json data for all defined services (`sara_services_data`)
 1. expand the CFengine variables for all service bundle data (`sara_services_data_expand`)
 1. run the service  with the specified json data `(sara_services_run`)
