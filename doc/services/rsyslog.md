# RSYSLOG

Source: [rsyslog.cf](/services/rsyslog.cf)

This bundle is used to configure the rsyslog daemon. The following actions are protected by a class:
 * Accept external log from other hosts (RSYSLOG_ACCEPT_REMOTE_LOG)

The class can be set in the def.cf/json:
```
"rsyslog": {
    "classes": {
        "ACCEPT_REMOTE_LOG": "any"
    }
}
```

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```
"vars": {
    "scl_services_enabled": [
        "...",
        "rsyslog",
        "..."
    ]
}
```

## def.cf/son

See [default.json](/templates/rsyslog/json/default.json) what the default values are and which variables can be overriden.
