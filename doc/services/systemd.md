# systemd

Source: [systemd.cf](/services/systemd.cf)

This bundle will generate overides for system.conf and user.conf in:
 * /etc/systemd/system.conf.d/surfsara.conf
 * /etc/systemd/user.conf.d/surfsara.conf

The defaults are empty and can overriden by `key = value` pair.

It can also handle the installation and deletion of systemd services The service
files are installed for:
 * debian, redhat, centos : /lib/systemd/system


## Usage

The bundle can be run via:
 *  `"" usebundle => systemd_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "systemd",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/systemd/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "systemd_json_files" slist => { "cpu_memory_accounting.json" };
```

The variable must be `systemd_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

f you want to debug this bundle set the `DEBUG_dhclient` class, eg:
 * `-DDEBUG_systemd`

## def.cf/json

See [default.json](/templates/systemd/json/default.json) what the default values are and
which variables can be overriden.

Here is an example how to use the bundle:
```json
{
"vars": {
    "systemd": {
        "system_conf": {
            "DefaultCPUAccounting": "yes",
            "DefaultMemoryAccounting": "yes"
        }
    }
}
```

### bundle systemd_install_and_check_service(service_file)

This bundles will install a systemd service file and check the service:
 * Copy the `service_file` from specified location
 * If its enabled, if not enable service
 * If its running, if not start service

### bundle systemd_disable_service(service_name)

Will disable the service name.
