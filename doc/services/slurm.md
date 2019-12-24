
# slurm

Source: [slurm.cf](/services/slurm.cf)

This bundle will generate these/this file(s) from mustache templates:
 * debian in `/etc/slurm-llnl`
 * centos in `/etc/slurm`
    * cgroup.conf
    * cgroup_allowed_devices_file.conf
    * gres.conf
    * nodes.conf
    * slurm.conf
    * slurmdb.conf
    * topology.conf

If one of the files is changed then the following ""class"" will be set:
 * sara_etc_slurm_llnl_slurm_conf (debian)
 * sara_etc_slurm_slurm_conf (centos)

The class name for the other files has the same format.

These templates are located in:
 * templates/slurm
 * templates/slurm/json

The following clases can be set via def.cf/json:
 * backup: When set make a backup of the SQL sever
 * client: Configure the machine as client
 * logrotate: When set disable logrotate configs and use cfengne logrotate (no daemons restart)
 * slurmd_disable: Disable the slurmd systemd service
 * server: Configure the machine as server
 * submit: Configure the machine as submit host

## Usage

The bundle can be run via:
 *  `"" usebundle => slurm_autorun();`
 * `def.sara_services_enabled` (prefered)
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "slurm",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/slurm/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "slurm_json_files" slist => { "cluster.json" };
```

The variable must be ""slurm_json_files"" and with this setup 1 extra json file will be merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_slurm` class, eg:
 * `-DDEBUG_slurm`

## def.cf/json

See [default.json](/templates/slurm/json/default.json) what the default values are and
which variables can be overriden.

Here are some examples how to use it:
 * specify resolv configuration in def.cf:
```
vars:
    "slurm_json_files" slist => { "cluster.json" };
```

 * override config settings in def.cf
```
vars:
    "slurm" data => parsejson( '{ "PrologFlags": "contains" }' );
```

 * override config settings in ''def.json'':
```json
"vars": {
    "slurm": {
            "json_files": [ "mona.json" ]
        },
}
```

 * override variable config setting in ''def.json''
```json
"slurm": {
    "PrologFlags": "contains"
}
```

### spank plugins

Slurm Plug-in architecture for Node and job Kontrol (SPANK)
plugins are configured via the `plugstack.conf` file.

This generations of the spank plugin configuration file(s) can be
done dynamically,eg:
```json
 "spank_templates": {
        "private_tmpdir.mustache" : {
            "dest": "$(slurm.plugstack_dir)/private-tmpdir.conf",
            "data": {
                "config": [
                    "required /opt/slurm/lib/private-tmpdir.so base=/scratch/slurm  mount=/var/tmp mount=/tmp mount=/scratch"
                ]
            }
        }
    },
```

This will generate the `private-tmpdir.conf` file with the aid of the specified mustache file and
json data.
