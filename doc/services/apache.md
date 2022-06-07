# apache.cf

Source[apache.cf](/services/apache.cf)

This bundle will be used to:
 * Configure Apache in /opt/apache so when updating/upgrading Apache there is no conflict with the distribution setup
 * Simplified setup so it is easier to use with CFEngine
 * Still free formatting of Apache code for modules and sites
 * Only configures Apache, for Python wsgi and/or PHP a separate service needs to be created
 * Clean sites.d directory if thee are files that are not specified with `sites`or `sites_generated`
 * Clean modules.d directory if thee are files that are not specified with `modules_standard` or `modules_extra`

Some site files may be generated om the machine like we do for JupyterHUB. We generate this file in the
`apache.sites_dir` variable. To prevent these file from deletion we need to specify this file(s) in the following
json variable:
 * `sites_generated`

Some modules require configuration files. This specified per module and the default can be overriden with json
variable `modules_extra`, eg:
```json
modules_extra: {
        jk: data/apache/cua/jk.conf,
        proxy_fcgi: data/apache/cua/proxy_fcgi.conf
},
```

Please note that this bundle has only been tested/designed for Debian Stretch or Buster and therefor a specific
Debian Apache file envvars wil be written to `/etc/apache2/envvars`. This is needed to make sure Apache daemon
uses the `/opt/apache/httpd.conf` instead of the default configuration file.

The following json variables can be set in def.cf/json to  invoke files bundles:
 * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

TODO:
 - Desining separate services for Python wsgi and PHP
 - A construction need to be design for easy switching between "Performance" profiles based on a value of the machine

## Usage

The bundle can be run via:
 *  `"" usebundle => apache_autorun();`
 * `def.scl_services_enabled` (prefered)
```json
"vars": {
    "scl_services_enabled": [
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
        "apache" slist => { "cua.json" };
```
