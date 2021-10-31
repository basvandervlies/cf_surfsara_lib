# jupyterhub

Source: [jupyterhub.cf](/services/jupyterhub.cf)


This will install the jupyterhub software on a node, and we can generate multiple jupyterhub setups on
one node. We mostly use jupyterhub to submit jobs to the batch system and start the notebook on a compute
node. Each jupyterhub has its own parameters for submitting jobs. Apache is used as frontend web server,
see [apache.cf](/services/apache.cf). This service bundle will also generate the apache configuration
file for you.

The following json variables can be set in def.cf/json to  invoke files bundles:
 * install_tarballs: See [files.cf](/masterfiles/lib/scl/files.cf)

The following clases can be set via def.cf/json:
 *  `RESTART_SCHEDULE`:  Restart the jupyterhub services on specified time(s), eg:
```
"RESTART_SCHEDULE": [
    "Hr00.Min00_05"
]
```

The definition of jupyterhub configuration is illustrated via an example:
```
    "config_default": {
        "admin_groups": [
            "'lisa_surfsara'"
        ],
        "dir": "$(sara_data.jupyterhub[dir])",
        "pam_service_file": "jupyterhub",
        "start_timeout": "3600"
    },
    "configs": [
        {
            "api_url": "http://127.0.0.1:8100",
            "bind_url": "http://127.0.0.1:8000",
            "hub_bind_url": "http://:8200",
            "name": "course",
            "template_file": "jupyterhub_course.mustache",
            "ws_url": "ws://127.0.0.1:8000"
        },
        {
            "api_url": "http://127.0.0.1:8101",
            "bind_url": "http://127.0.0.1:8001",
            "hub_bind_url": "http://:8201",
            "name": "prod",
            "pam_service_file": "jupyterhub-prod",
            "template_file": "jupyterhub_prod.mustache",
            "ws_url": "ws://127.0.0.1:8001"
        }
    ],
```

The `config_default` section are variables used in all jupyterhub configuration files but you can
override them in the `configs` section. In the `configs` section there are 2 jupyterhub
configurations defined:
 * course
 * prod

Each configuration must use a unique port number. For the jupyterhub `course` it will generate:
 * /etc/systemd/system/jupyterhub-course unit file
 * /opt/jupyterhub/bin/course.sh (used in the systemd unit file)
 * /opt/jupyterhub/etc/course.py (with the aid of `jupyterhub-course.mustache`)
 * start the jupyterhub server
 * This is also done for the other configurarions.

The apache configurarion is also generated with the right port numbers and apache will be restarted. Now
the jupyterhubs are accessible with:
 * https://<name>/course
 * https://<name>/prod

## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "jupyterhub",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/jupyterhub/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "jupyterhub_json_files" slist => { "lisa.json" };
```

The variable must be ''jupyterhub_json_files'' and with this setup 1 extra json file will be merged.

### DEBUG

If you want to debug this bundle set the `DEBUG_jupyterhub` class, eg:
 * `-DDEBUG_jupyterhub`

## def.cf/json

See [default.json](/templates/jupyterhub/json/default.json) what the default values are and
which variables can be overriden.

Here is an example to check also the apache binary (def.cf):
```
vars:
    "jupyterhub_json_files" slist => { "lisa.json" ];
```

Same can also be set in `def.json`
```json
"vars": {
    "jupyterhub": {
        "dir": "/sw/jupyterhub"
    }
}
