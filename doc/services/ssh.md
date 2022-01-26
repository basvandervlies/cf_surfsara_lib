# ssh

Source: [ssh.cf](/services/ssh.cf)

This bundle installs ssh software on a node.

This bundle will generate these files from mustache templates:
 * /etc/ssh/ssh.conf
 * /etc/ssh/sshd_banner
 * /etc/ssh/sshd.conf

If one of the files is changed then the following ''class'' will be set:
 * scl_etc_ssh_ssh_conf
 * scl_etc_ssh_sshd_banner
 * scl_etc_ssh_sshd_conf

These files will be generated with the aid of mustache templates with json data.
the templates are located in:
 * templates/ssh/
 * templates/ssh/json

The following clases can be set via def.cf/json:
 *  `USE_DEPRICATED_OPTIONS`:  These options are not used anymore
 *  `KEYGEN`: Must we generate host keys, command line options can bet set via json
 *  `PUBKEY_AUTHENTICATION`: Enable ssh public keys via `AuthorizedKeysCommand`

The following json variables can be set in def.cf/json to  invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/scl/files.cf)
 * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "ssh",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/ssh/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "ssh_json_files" slist => { "policy_server.json" };
```

The variable must be ''ssh_json_files'' and with this setup 1 extra json file will be  merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_ssh` class, eg:
 * `-DDEBUG_ssh`

## def.cf/json

See [default.json](/templates/ssh/json/default.json) what the default values are and
which variables can be overriden

Here are some examples how to use it:

 * specify ssh configuration in def.cf:
```
vars:
    "ssh_json_files" slist => { "policy_server.json" };
```

 * Set/Override the daemon options variable in ''def.json'':
```json
        "ssh" : {
            "classes": {
                "keygen": [ "any" ]
            },
            "MaxAuthTries": "6",
            "X11Forwarding": "no",
        },
```

 * override server setting in def.cf
```
vars:
    "ssh" data => parsejson( '{ "X11Forwarding":  "no"  }' );
```

### copy_files

When this variable is set it will copy the specified file to the `ssh.config_dir`, eg:
```json
"copy_files": [
    {
        "dest": "$(ssh.config_dir)/shosts.equiv",
        "source": "cf_bundles_dir/ssh/lisa/shosts.equiv",
        "mog": [ "0644", "root", "root" ]
    },
    {
        "dest": "$(ssh.config_dir)/ssh_known_hosts2",
        "source": "cf_bundles_dir/ssh/lisa/ssh_known_hosts2",
        "mog": [ "0644", "root", "root" ],
        "run_bundle": "ssh_daemons_restart"
    }
]
```
where `cf_bundles_dir` is a ''cf-serverd shortcut''.
