# apt.cf

Source: [apt.cf](/services/apt.cf)

This bundle will be used to:
 * generate repository files in /etc/apt/sources.list.d
 * generate /etc/apt/auth.conf, via the json variable `auth_conf`
 * install apt packages
 * Copy the gpg key files if `key_file` is set for repository (prefered method)
 * Fetch the gpg key via `apt_import_key` command if `repo_key` is set for repository

The following actions are proctected by a class:
 * automatic install security uodate (AUTOMATIC_SECURITY_UPDATE)
 * automatic remove obsolete packages (AUTOREMOVE)
 * check the status of the package manager (CHECK_STATUS)
 * kill apt/aptitude processes that run more then 1 hour (KILL_PKG_MANAGER)
 * Check the debian release and upgrade if needed (OS_VERSION_CHECK)
 * setting debconf values for package field(s), controlled via  json data
 * disable systemctl timer services for apt, may interfere with cfengine (APT_SYSTEMD_DISABLE)
 * remove /etc/apt/sources.list file (SOURCES_FILE_REMOVE)
 * automatic perform an dist-upgrade (DIST_UPGRADE)



The classes can be set in def.cf/json:
```json
{
"apt": {
    "classes": {
        "AUTOMATIC_SECURITY_UPDATE": "any",
        "AUTOREMOVE": "any",
        "SYSTEMD_DISABLE": "systemd"
    }
}
```

The `/etc/apt/auth.conf` is default empty. But we can configure it for password enabled repositories via `auth.conf`:
```
"auth_conf": [
    {
        "login": "readonly",
        "machine": "example.surfsara.nl/artifactory/CC-Debian-",
        "password": "secret"
    }
]
```
                                                            ]
## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
"vars": {
    "sara_services_enabled": [
            "...",
            "apt",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/apt/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "apt_json_files" slist => { "debconf.json" };
```

The variable must be `apt_json_files` and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_apt` class, eg:
 * `-DDEBUG_apt`

## def.cf/json

See [default.json](/templates/apt/json/default.json) what the default values are and
which variables can be overriden.

The following must be set in host specific host file: `def.json`
```json
"vars": {
    "apt": {
        "classes": {
            "AUTOMATIC_SECURITY_UPDATE": "any"
        },
        "json_files": [ "debconf.json" ]
    }
}
```

### Debconf

Debconf is a configuration system for Debian packages. When `debconf` json data
is set. The apt bundle will configure the package with these values, eg:
```json
{
    "debconf": [
        {
            "package": "dash",
            "field": "dash/sh",
            "type": "boolean",
            "value": "false"
        },
        {
            "package": "debconf",
            "field": "debconf/frontend",
            "type": "select",
            "value": "Noninteractive"
        }
    ]
}
```

This will configure the packages `dash` and `debconf`.
