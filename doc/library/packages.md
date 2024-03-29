# packages.cf

Source: [packages.cf](/masterfiles/lib/scl/packages.cf)

## scl_service_packages
This bundle handles  the service packages. The service bundle defines the default
package(s) to install/remove and/or purge. It can be overriden with json data, eg:
```
"vars": {
    "ssh": {
        "packages": {
            "install": {
                "openssh-server": "[version]"
            }
        }
    }
}
```

This will install the 'openssh-server' package and override the package definitions in the service bundle ssh.
The `[version]` can be empty `""`. It will install any version of the package.

Another example is to install and remove ssh packages in 'def.json':
```
"vars": {
    "ssh": {
        "packages": {
            "install": {
                "openssh-server": "[version]"
            },
            "remove": {
                "openssh-blacklist": "[version]"
            }
        }
    }
}
```

The following package actions are implemented:
 * install: install the package
 * install_backports: install the package from backports (Debian)
 * purge: delete the package and all it configuration files
 * remove: delete the package and preserve the configuration files
