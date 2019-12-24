# apache.cf

Source[apache.cf](/services/apache.cf)

This bundle will be used to:
 * Configure Apache in /opt/apache so when updating/upgrading Apache you won't conflict with the distribution setup
 * Simplified setup so it's easier to use with CFEngine
 * Still free formatting of Apache code for modules and sites
 * Only configures Apache, for Python wsgi and/or PHP a separate service needs to be created

Please note that this bundle has only been tested/designed for Debian Stretch or Buster. And therefor a specific
Debian Apache file envvars wil be written to `/etc/apache2/envvars`. This is needed to make sure Apache daemon
uses the `/opt/apache/httpd.conf` instead of the default configuration file.

TODO:
 - Desining separate services for Python wsgi and PHP
 - A construction need to be design for easy switching between "Performance" profiles based on a value of the machine

## Usage

The bundle can be run via:
 *  `"" usebundle => apache_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "apache",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/apache/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "apt_json_files" slist => { "debconf.json" };
```
