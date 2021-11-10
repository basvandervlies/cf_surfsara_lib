# PKG_MANAGEMENT

Source: [pkg_management.cf](/services/pkg_management.cf)


This will install/delete packages that are not installed via other services files. You just want to make sure that some handy
utitlities are installed or some anonying utilities are removed, eg:
```json
{
    "jq": {
        "action": "install",
        "version": ""
    },
    "pip": {
        "action": "remove"
        "version": ""
    }
}
```

This will:
  * install the package `jq` and does not care about which `version` is installed
  * remove the package `pip` and does not care about which `version` must be removed

The following actions are supported:
 * install
 * remove
 * purge is only supported on Debian like systems on other systems it will be `remove`
 * install_backports is only supported Debian like systems on other systems it will be `install`


The following classes can be se via def.cf/json:
 * `PRIO_BACKPORTS`: This will priorize the `backports` packages above the `stable` ones

When `PRIO_BACKPORTS` class is set the following file will be created with the aid of
`inline_mustache`:
  * `/etc/apt/preferences.d/99-surfsara` (overridable via json file)

The `backports` package will now be considers as `stable` package. The upgrade of `backport`
package is the same as `stable` package:
 * `apt  --simulate --ignore-hold upgrade`

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "pkg_management",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/pkg_management/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "pkg_management_json_files" slist => { "lisa.json" };
```

The variable must be ''pkg_management_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_pkg_management` class, eg:
 * `-DDEBUG_pkg_management`

##  def.cf/json

See [default.json](/templates/pkg_management/json/default.json) what the default values are and
which variables can be overridden.

Here are some examples how to use it:
 * specify pkg_management configuration in def.cf:
```
vars:
    "pkg_management_json_files" slist => { "lisa.json" };
```

 * Set/Override variables in *def.json*:
```json
"pkg_management": {
    "json_files": [ "lisa.json" ],
    "jq": {
        "action": "install",
        "version": ""
    }
}
```
