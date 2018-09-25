<!-- vim-markdown-toc GFM -->

* [Version: 0.9.8 (2018-09-25)](#version-098-2018-09-25)
    * [apt](#apt)
    * [munge](#munge)
* [Version: 0.9.4 (2018-09-07)](#version-094-2018-09-07)
* [Version: 0.9.0 (2018-08-24)](#version-090-2018-08-24)

<!-- vim-markdown-toc -->
# Version: 0.9.8 (2018-09-25)

 * Services added: apt, munge
 * Only copy local files if  hashes differ, use  `local_dcp` instead of `local_cp`
 * Reduce the verbose output for local file(s) copy only to the debugged bundle
 * Show json files used  when using `<bundle_name>_local_generated_json_files` option
 * Fix systemd permission problem for user configuration settings, must be readable for everybody
 * Added surfsara modules directory `$(sys.workdir)/modules/surfsara` in `mpf_installation` script:
   * `apt_import_key`: Needed by apt bundle to import the repository key
   * `debconf`: Needed by apt bundle to set package options

## apt

The services can do a lot of action.  Most actions are protected by a class statement. The following actions are
defined:
 * generate repository files in /etc/apt/sources.list.d
 * install apt packages
 * automatic install security uodate (`AUTOMATIC_SECURITY_UPDATE`)
 * automatic remove obsolete packages (`AUTOREMOVE`)
 * check the status of the package manager (`CHECK_STATUS`)
 * kill apt/aptitude processes that run more then 1 hour (`KILL_PKG_MANAGER`)
 * Check the debian release and upgrade if needed (`OS_VERSION_CHECK`)
 * setting debconf values for package field(s), controlled via  json data
 * disable systemctl timer services for apt, may interfere with cfengine (`SYSTEMD_DISABLE`)
 * remove /etc/apt/sources.list file (`SOURCES_FILE_REMOVE`)

## munge

MUNGE is an authentication service for creating and validating credentials:
 * https://dun.github.io/munge/

#  Version: 0.9.4 (2018-09-07)
  * Bug fixed in:  `sara_service_copy_dirs` bundle, forgot to set `compare` value, default: `digest`
  * Bug fixed when using `-DTEMPLATE_LOCAL_COPY` cf-agent flag. Path to find templates dir was wrong.
  * Added services:
    * nvidia_gpu_prometheus_exporter
    * slurm_prometheus_exporter


#  Version: 0.9.0 (2018-08-24)
  * New services added: cron, pam, pam\_radius, nscd, node\_exporter, sudo and systemd.
  * Added installation script for MPF: `mpf_installation`, cfengine version tested: 3.10,3.11 and 3.12
  * Added SURFsara autorun services setup, controlled via `def.sara_services_enabled`
  * Skip mustache expand if not a valid destination
  * Use standard cfengine `remote_dcp` bundle instead of `sara_hash_no_perms_cp`
  * Force local copy of mustache/json file(s) with `-DTEMPLATE_LOCAL_COPY`, `-DMUSTACHE_LOCAL_COPY` or `-DJSON_LOCAL_COPY`
  * Report if we can not copy the specified json file(s) for a  bundle.
  * Can now set bundle classes based on an cfengine expression in the bundle json data, ala def.json, This will set the class `DHCLIENT_RESOLV_CONF` on host `r24n2`:
 ```
"dhclient": {
  "classes": {
     "RESOLV_CONF": "r24n2"
  }
},
```
  * Service packages defined in the bundle can now be overridden by 'def.json'. The values can be `install/remove/purge`.
  * Implemented `copy_files` json for services, see `ssh` changes. bundle name: `sara_service_copy_files`
  * Implemented `copy_dirs` json for services, see `node_exporter` bundle name: `sara_service_copy_dirs`

## service packages override options

The following example will install any version of `openssh-client` and the latest version of `openssh-blacklist`.
```
"ssh": {
  "packages": {
        "install": {
            "openssh-client" : "",
            "openssh-blacklist" : "latest",
        }
    }
    ....
},
```

The next one will install `openssh-client` package and remove the `openssh-blacklist` package:
```
"ssh": {
  "packages": {
        "install": {
            "openssh-client" : ""
        },
        "remove": {
            "openssh-blacklist" : ""
        }
    }
    ....
},
```
## autorun surfsara services

If `autorun` is enabled in the MPF framework. You can control which service file(s) are included, eg:
```
{
  "vars": {
    "sara_services_enabled" : [ "ssh", "ntp" ]
  }
}
```

This will include the service files `services/surfsara/ssh.cf` and `services/surfsara/ntp.cf`
and run/configure the ssh/ntp services with the aid of mustache/json data. The bundle run can
be protected by a class statement (def.json)  default is `any`, eg:
```
"vars": {
    "sara_services_enabled" : [ "ssh", "ntp" ]
}
}
"ssh": {
    "run_class": "debian|centos"
}

This will run the ssh service only for debian and centos hosts.
```

### Own Framwork

The default setting for `sara_services_dir` is `services/surfsara`.  If you copied the
surfsara services files to another location you must set the `def.sara_services_dir`
variable.

In your framework call the following bundle and see above for `def.json` example:
```
methods:
    "" usebundle => sara_services_autorun();
```

## node\_exporter

This is  a new bundle "Prometheus exporter for hardware and OS metrics exposed by *NIX kernels". The bundle
make use of a new feature `sara_service_copy_dirs`, eg:
```
    "copy_dirs": [
        {
            "dest": "$(sara_data.node_exporter[dir])",
            "exclude_dirs": [ ".git", ".svn" ],
            "purge": "true",
            "run_bundle": "node_exporter_restart",
            "source": "cf_bundles_dir/prometheus_exporters/node_exporter-0.15.2"
        }
    ],
```

This will copy the directory and make sure that the destination is  exac the same as the source.
the default option for `copy_dirs` are:
```
bundle agent sara_cp_dir_default
{
    vars:
       any::
            "attributes" data => parsejson('{
                "compare": "digest",
                "preserve": "true",
                "purge": "false",
                "sync": "false",
                "type_check": "false"
                }');
}
```

## ssh changes
   * Use the `sara_service_copy_files` bundle
```
 "ssh": {
    "copy_files": [
        {
            "dest": "$(ssh.config_dir)/ssh_host_dsa_key",
            "src": "cf_bundles_dir/ssh/doornode/ssh_host_dsa_key",
            "mode": "0600", "owner": "root", "group": "root",
            "run_bundle": "ssh_daemon_restart"
        },
        {
            "dest": "$(ssh.config_dir)/ssh_host_dsa_key.pub",
            "src": "cf_bundles_dir/ssh/doornode/ssh_host_dsa_key.pub",
            "mode": "0644", "owner": "root", "group": "root",
            "run_bundle": "ssh_daemon_restart"
        }
    ]
},
```

   * Some ssh options are deprecated.  If you want to include this options in `sshd_config` you must set the class `SSH_USE_DEPRICATED_OPTIONS`, it is default enabled for debian_7 and centos.
```
vars:
    "ssh" data => parsejson( '{ "classes": { "USE_DEPRICATED_OPTIONS" : "any" }  }' );

or

classes:
    "SSH_USE_DEPRICATED_OPTIONS" expression => "any";
```

   * default ssh options added:
```
    "X11Forwarding": "yes",
    "X11UseLocalhost": "yes
```

    * Added a new  option generate  host keys controlled via the class `SSH_KEYGEN` default not set, aen default option:
```
    "keygen_opt": "-A"
```

## postfix changes

 * Added functionallity to enable `virtual_alias_maps` entry in postfix main.cf. The following example will copy the mustache template file from `templates/postfix/ldap_aliases_map.mustache` and expand it with the specified inline json data:
```
"classes" : {
    "VIRTUAL_MAPS": [ "mta.example.com" ],:
},
"virtual_alias_maps": {
    "ldap_aliases_map.mustache" : {
        "delimiter": ":",
        "dest": "/etc/postfix/virtual_alias_maps.cf",
        "protocol": "ldap",
        "data": {
            "bind" : true,
            "bind_options" : {
                "dn" : "<your_dn>",
                "pw" : "<your_bind_password>"
            },
            "port": "636",
            "query_filter" : "(uid=%s)",
            "result_attribute" : "mail",
            "search_base" : "ou=Users,dc=example,dc=com",
            "server": "ldaps://ldap.example.com"
        }
    }
}
```

 # 18-Oct-2017
  * Added dhclient.cf service,  for now only disable resolv.conf generation.
  * Added check\_space.cf service, monitor filesystem and you can execute an command or bundle if promise has failed
  * postfix template can now handle: virtual\_mailbox\_limit  option (Lucas Slim, SURFsara)
  * library improvements, sara\_data\_autorun is inline with sara\_mustache\_autorun,. Simplified a lot of coce.
  * Added services.cf to library as alternative for autorun. All methods are protected by a class sercice name. (Dennis Stam, SURFsara)

