# configurable_http_proxy

Source: [configurable_http_proxy.cf](/services/configurable_http_proxy.cf)


This will install the configurable-http-proxy software on a node. This software is used by [jupyterhub.cf](/services/jupyterhub.cf)

The following json variables can be set in def.cf/json to  invoke files bundles:
 * install_tarballs: See [files.cf](/masterfiles/lib/scl/files.cf)

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "configurable_http_proxy",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/configurable_http_proxy/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "configurable_http_proxy_json_files" slist => { "lisa_hub.json" };
```

The variable must be ''configurable_http_proxy_json_files'' and with this setup 1 extra json file will be merged.

### DEBUG

If you want to debug this bundle set the `DEBUG_configurable_http_proxy` class, eg:
 * `-DDEBUG_configurable_http_proxy`

## def.cf/json

See [default.json](/templates/configurable_http_proxy/json/default.json) what the default values are and
which variables can be overriden.

Here is an example to check also the apache binary (def.cf):
```
vars:
    "configurable_http_proxy_json_files" slist => { "lisa_hub.json" ];
```

Same can also be set in `def.json`
```json
"vars": {
    "configurable_http_proxy": {
        "dir": "/sw/configurable_http_proxy"
    }
}
