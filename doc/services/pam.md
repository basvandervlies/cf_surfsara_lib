# pam

Source: [pam.cf](/services/pam.cf)

This bundle will generate pam files in `$(pam.dir):/etc/pam.d` with the aid of
inline mustache template. Only work for cfengine => 3.12.

if one of the files is changed then the following **class** will be set:
 * `sara_etc_pam_d_<filename>`

The bundle can also generate configuration files for, class are set with `if_repaired("sara$(file)")`:
 * pam_limits --> /etc/security/limits.d/scl.conf
 * pam_listfiles --> /etc/security/<section>.<allow|deny>, section can be `group|user`

These files will be generated with the  json data specified
the templates are located in:
 * templates/pam/
 * templates/pam/json

The following json variables can be set in def.cf/json to invoke files bundles:
 * copy_files: See [files.cf](/masterfiles/lib/surfsara/files.cf)
 * copy_dirs: See [files.cf](/masterfiles/lib/surfsara/files.cf)

## Usage

The bundle can be run via:
 * `def.sara_services_enabled`
```json
"vars": {
    "sara_services_enabled": [
            "...",
            "pam",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/pam/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "pam_json_files" slist => { "ldap.json", "ldap_pam.json" };
```

The variable must be ''pam_json_files'' and with this setup 2 extra json file will be  merged.

### Debug

If you want to debug these bundle set the `DEBUG_pam` class, eg:
 * `-DDEBUG_pam`


## Def.cf/json

See [default.json](/templates/pam/json/default.json) what the default values are and
which variables can be overriden. This are all data section definitions.  To couple a data
section to a pam file, see [default_pam.json](/templates/pam/json/default_pam.json) for debian|centos.

### pam configuration files

To generate the configuration files for the pam module. We use inline json to trigger the generation:

#### pam_listfile

For we can generate the `group.allow|deny` in /etc/security with the following json file:
```json
    pam_listfile: {
        group: {
            allow: [
                lisa_surfsara,
                surftraining_student_jupyter_ssh,
                surftraining_teacher
            ],
            deny:  [
                sw_lisa
           ]
        }
    }
}
```

The pam module configuration line:
 * `account required pam_listfile.so onerr=fail item=group sense=allow file=/etc/security/group.allow`


#### pam_limits

If data section is defined we generate the following file `/etc/security/limit.d/scl.conf`, eg:
```json

    "pam_limits": [
        "# Managed by CFEngine",
        "#",
        "*       soft    nofile      4096",
        "*       hard    nofile      8192",
        "*       soft    memlock     unlimited",
        "*       hard    memlock     unlimited",
        "*       soft    stack       unlimited",
        "*       hard    stack       unlimited",
        "root       soft    nofile      4096",
        "root       hard    nofile      8192",
        "root       soft    memlock     unlimited",
        "root       hard    memlock     unlimited",
        "root       soft    stack       unlimited",
        "root       hard    stack       unlimited"
    ]
}
```

### Examples
Here are some examples how to use it:
 * specify pam configuration in def.cf:
```
vars:
    "pam_json_files" slist => { "default_pam.json" };
```
 * Define pam data stack in *pam/templates/json/scl.json*:
```json
"common-account": {
    "comment": "only local logins",
    "rules": [
        "account [success=1 new_authtok_reqd=done default=ignore]   pam_unix.so",
        "account requisite                                          pam_deny.so",
        "account required                                           pam_permit.so"
    ]
},
"common-auth": {
    "comment": "only local logins",
    "rules": [
        "auth [success=1 default=ignore]    pam_unix.so nullok_secure",
        "auth requisite                     pam_deny.so",
        "auth required                      pam_permit.so"
    ]
},
"common-password": {
    "comment": "only local logins",
    "rules": [
        "auth [success=1 default=ignore]    pam_unix.so obscure sha512",
        "auth requisite pam_deny.so",
        "auth required pam_permit.so"
    ]
},
"common-session": {
    "comment": "only local logins",
    "rules": [
        "session [default=1]     pam_permit.so",
        "session requisite       pam_deny.so",
        "session required        pam_permit.so",
        "session required        pam_unix.so"
    ]
},
"common-session-noninteractive": {
    "comment": "only local logins",
    "rules": [
        "session [default=1]     pam_permit.so",
        "session requisite       pam_deny.so",
        "session required        pam_permit.so",
        "session required        pam_unix.so"
    ]
},
"system-auth": [
    "#",
    "# Auth section",
    "#",
    "auth        required      pam_env.so",
    "auth        required      pam_faildelay.so delay=2000000",
    "auth        sufficient    pam_fprintd.so",
    "auth        sufficient    pam_unix.so nullok try_first_pass",
    "auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success",
    "auth        required      pam_deny.so",
    "#",
    "# Account section",
    "#",
    "account     required      pam_unix.so",
    "account     sufficient    pam_localuser.so",
    "account     sufficient    pam_succeed_if.so uid < 1000 quiet",
    "account     required      pam_permit.so",
    "#",
    "# Password section",
    "#",
    "password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=",
    "password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok",
    "password    required      pam_deny.so",
    "#",
    "# Session section",
    "#",
    "session     optional      pam_keyinit.so revoke",
    "session     required      pam_limits.so",
    "session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid",
    "session     required      pam_unix.so"
]
```

 * create the pam files with the above  pam data definitions
```
vars:
    "pam" data => parsejson('{
        centos: {
            "$(pam.dir)/system-auth": "system-auth"
        },
        debian: {
            "$(pam.dir)/common-account": "common-account",
            "$(pam.dir)/common-auth": "common-auth",
            "$(pam.dir)/common-password": "common-password",
            "$(pam.dir)/common-session": "common-session",
            "$(pam.dir)/common-session-noninteractive": "common-session-noninteractive"
        }
    }');
```

  * You can create your own data section and generate your own pam files, eg:
```json
"vars": {
    "pam": {
        "sudo-radius": {
            "comment": "radius",
            "rules": [
                "auth required pam_radius_auth.so skip_passwd retry=1"
            ]
        },
        "debian": {
            "$(pam.dir)/sudo": "sudo-radius"
        }
    }
}
```

