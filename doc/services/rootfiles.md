# ROOTFILES

Source: [rootfiles.cf](/services/rootfiles.cf)

This bundle will copy important file(s) or/and dirs  to `/root` directory. It
will generate when json data is set:
 *  /root/.ssh/authorized_keys

You can control some behaviours of the ROOTFILES bundle via classes:
```json
"rootfiles": {
    "classes": {
        "NO_MODULE_LOAD": "any"
    }
}
```
Prevent loading of standard modules:
  * ROOTFILES_NO_MODULE_LOAD : Will create `/root/.no_module_load` file

The following json variables can be set in def.cf/json to invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/scl/files.cf)
 * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```
"vars": {
    "scl_services_enabled": [
            "...",
            "rootfiles",
            "..."
    ]
}
```

The bundle will always read the [default.json](/templates/rootfiles/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "rootfiles_json_files" slist => { "surf.json" };
```

The variable must be `root_files_json_files` and with this setup 1 extra json file will be merged.

### DEBUG

If you want to debug these bundle set the `DEBUG_rootfiles` class, eg:
 * `-DDEBUG_rootfiles`



## def.cf/json

See [default.json](/templates/rootfiles/json/default.json) what the default values are and
which variables can be overriden.

Some example show to us it:
 * Generate `authorized_keys` file with stepping stone public key:
```
{
    "vars": {
        "rootfiles": {
            "stepping_stone_authorized_keys": [
                {
                "key_type": "ssh-rsa",
                "key": "AAAAB3NzaC1yc2EAAAADAQABAAABAQCh+gjYr/XrrRyvih2V3u7RDMZMQE0NnJr3EU717lcRQ0ae9EZxn6lPYiG4xJoYLmSg885zYKTxff/fVMZYfHzLtzLylUhup8RP1XAAuiVcXMffFqE9mau+FpE2W6bEmtsxs/OboQ/AOfBtl1Lpghol0oM7kaYzZo4OBu39sJaLZIfB0Z1NPp8PVBgeDFxBlyfYDkeGIDAGltO8NsY+Di0QWFyfJVmLPzUllvu4tCs5XoK7zcFOnVUAlflDbEhaSAzll4J7yE1Eatl7Fx68m4uAAWVt0m0xqfWcoHKnKfmSPN94DiZnRyn81UR6rOAklNHNBqg+Pps5n7Ow8BBHBKFp",
                "comment": "root@install1.cc.surfsara.nl"
                }
            ]
        }
    }
}
```

### COPY_DIRS

When this variabele is set it will copy the directoy to the specified destination and can run a bundle
if there are changes, eg:
```
"copy_dirs": [
    {
        "dest": "/root/.subversion",
        "exclude_dirs": [ ".git", ".svn" ],
        "source": "data/rootfiles/surfsara/.subversion"
    }
]
```

### COPY_FILES

When this variable is set it will copy the specified file(s) to the
specified destination, example:
```
copy_files: [
    {
        "dest": "/root/.bashrc",
        "source": "data/rootfiles/surfsara/root/.bashrc",
        "mog": [ "0600", "root", "root"]
    },
    {
        "dest": "/root/dynmotd.sh",
        "source": "data/rootfiles/surfsara/root/dynmotd.sh",
        "mog": [ "0700", "root", "root"]
    }
]
```

### SSH_KEYS

With this variable is set it will copy the specified ssh keys to `/root/.ssh`,
example:
```
ssh_keys: {
    "source": "data/rootfiles/ssh_keys/2fa_web",
    "keys": [ "id_ed25519", "id_rsa" ]
}
```

### user_ssh_keys_dir

When this variable is set we copy user private/public keys to the `/root/.ssh` directory. These are user keys for an user that can become root
but want to use their own git repositories. Use `ssh-add -t 4h /root/.ssh/root-bas` in combo with `ssh-agent`, eg:
```
"user_ssh_keys_dir": "data/rootfiles/ssh_keys/git_user"
```
