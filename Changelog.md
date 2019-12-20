<!-- vim-markdown-toc GFM -->

* [Version: 0.9.XX (2019-12-XX)](#version-09xx-2019-12-xx)
* [Version: 0.9.50 (2019-05-28)](#version-0950-2019-05-28)
    * [INVENTORY modules](#inventory-modules)
* [Version: 0.9.20 (2018-12-20)](#version-0920-2018-12-20)
    * [Expand CFengine variables found in countainer (2 levels)](#expand-cfengine-variables-found-in-countainer-2-levels)
    * [SLURM](#slurm)
    * [NHC (Node Health Check)](#nhc-node-health-check)
    * [Bundle services data](#bundle-services-data)
* [Version: 0.9.8 (2018-09-25)](#version-098-2018-09-25)
    * [apt](#apt)
    * [munge](#munge)
* [Version: 0.9.4 (2018-09-07)](#version-094-2018-09-07)
* [Version: 0.9.0 (2018-08-24)](#version-090-2018-08-24)

<!-- vim-markdown-toc -->
# Version: 0.9.XX (2019-12-XX)
 * Services added: apache2, chrony
 * apt service changes:
    * merging order is `apt_repo_files` and then `apt[repo_files]`
    * Can handle gpg key files copy via json variable:
```#json
{
   "openldap_ltb": {
        "key_file": "openldap-ltb.asc",
        "repo": [
            {
                "name": "Openldap_ltb_repo",
                "desc": "openldap LTB packages project",
                "url": "deb https://ltb-project.org/debian/$(apt.os_name) $(apt.os_name) main"
            }
        ]
    }
```
 * pam service changes:
    * Added `copy_dirs` functionality
    * Added `install_packages` functionality
 * rootfiles service changes:
    * make sure /root has restricted permisions (0700)
    * Can now handle the root ssh keys:
```#json
"ssh_keys": {
    "source": "<dir>"
    "keys": [
        "id_rsa"
    ]
```
 * slurm service changes:
    * install packages based on roles roles packages\_server, packages\_client,  packages\_submit
    * `pid_dir` is now configurable
    * added class `SLURM_LOGROTATE` class. Use cfengine logrotate functionality
 * added some new bodies:
    * `body copy_from sara_sync_no_perms_cp`
    * `body link_from sara_relative_ln_s`

# Version: 0.9.50 (2019-05-28)
 * Services added: rsyslog
 * added SuSe (sles) support for: ntp, postfix, ssh
 * apt service changes:
   * Added meta tags , now we can start the service with  `def.sara_services_enabled` and autorun method
   * Rewrote apt\_check\_status bundle.  Check the package manager status and try to fix it if not healty
   * rename `apt_repository_json_files` to `apt_repo_json_files`
   * packge `dirmngr` is required
 * `sara_service_copy_dirs` default exclude dirs are `.git` and `.svn`. Can be overriden by json data.
 * `sara_service_packages` can now handle debian backports packages, eg:
```#json
{
"ssh": {
    "packages": {
        "install_backports": {
            "openssh-server': ""
        }
    }
}
```
 * ssh service changes:
   * Added a new option: `Banner_system_warning`
   * Added a new class `SSH_PUBKEY_AUTHENTICATION` for public key authentication via `authorized_keys_command` command
 * slurm service changes:
   * removed surfsara specific settings
   * add new class `SLURMD_DISABLE`
   * debian disable purging of packages
 * inventory module support added to the library. The modules are run before the services
 * Fixed some json format errors in `default.json` for services sudo and  dhclient

## INVENTORY modules

You can determine which cfengine modules to run. For the module protocol see:
 * https://docs.cfengine.com/docs/master/reference-promise-types-commands.html#module
```
CFEngine modules are commands that support a simple protocol in order to set additional variables
and classes on execution from user defined code. Modules are intended for use as system probes
rather than additional configuration promises. Such a module may be written in any language
```

Modulea included are:
 * surfsara/dmidecode
 * surfsara/lscpu

In `def.json` you can determine which modules to run with optional arguments:
 * `args`: Arguments supplied to the module command (Optional)
 * `run_class`: Only run module if this class condition is met (Optional)
 * `run_bundle`: Run CFengine bundle when module command has been run succesful.

A  `def.json` example:
```json
"sara_inventory_modules": [
        "surfsara/lscpu",
        "surfsara/dmidecode"
],
"surfsara/lscpu": {
        "args": "$(sara_inventory.cache_dir)"
},
"surfsara/dmidecode": {
    "args": "--output $(sara_inventory.cache_dir)/dmidecode.json --cf",
    "run_class": "debian|centos",
    "run_bundle": "sara_dmidecode_example"
}
```

# Version: 0.9.20 (2018-12-20)

 * Services added:  nsswitch, nhc, slurm
 * All service/library documentation is now online
    * https://github.com/basvandervlies/cf_surfsara_lib/blob/master/doc/library.md
    * https://github.com/basvandervlies/cf_surfsara_lib/blob/master/doc/services.md
 * apt service enhancements:
   * autoremove added option `-y` to skip questions
 * Munge service enhancements:
   * Remove string option to error prune
   * key file must be owned by user/group: munge
   * Daemon check was wrong
 * Node\_exporter, slurm\_prometheus\_exporter service enhancements:
   * init.d/systemd fixes
   * rewrote to comply with the new standard
 * Pam service enhancements:
   * pam\_listfile can not contain comments `#`
 * sara\_service\_copy\_dirs added mog option:, eg
   * `mog : [ "0755", "root", "root" ]` # will set all dir/files to this mode
   * changed the default of copy attribute `preserve` to `false` instead of `true`
   * silence the verbose information when `files_single_copy` is set
 * sara\_service\_copy\_files uses the same  keywords as  the dirs version:
   * `src` is now renamed to `source`
   * keywords `mode`, `owner` and `group` is replaced by `mog` keyword.
   * Note this is a incompatible change. All files  have been converted to new format
 * Always copy json/mustache files, do not check the type of the file.

## Expand CFengine variables found in countainer (2 levels)

It can now handle 2 level expansion, eg:
```#json
"copy_files": [
            {
                "dest": "$(sara_data.nvidia[dir])/$(sara_data.nvidia[script])",
                "source": "cf_bundles_dir/nvidia/$(sara_data.nvidia[script])",
                "mode": "0750", "owner": "root", "group": "root"
            }
]
"script": "NVIDIA-Linux-x86_64-$(sara_data.nvidia[version]).run",
"version": "410.57"
```

this will resolve `source` to : `cf_bundles_dir/nvidia/NVIDIA-Linux-x86_64-410.57.run`.

## SLURM

SchedMD® is the core company behind the Slurm workload manager software, a free
open-source workload manager designed specifically to satisfy the demanding needs
of high performance computing. Slurm is in widespread use at government laboratories,
universities and companies world wide. As of the June 2017 Top 500 computer list,
Slurm was performing workload management on six of the ten most powerful computers in
the world including the number 1 system, Sunway TaihuLight with 10,649,600 computing
cores, making it the preferred choice for workload management on the top ten computers
in the world.
 * https://www.schedmd.com/
                                                        },
## NHC (Node Health Check)

TORQUE, SLURM, and other schedulers/resource managers provide for a periodic
"node health check" to be performed on each compute node to verify that the
node is working properly. Nodes which are determined to be "unhealthy" can
be marked as down or offline so as to prevent jobs from being scheduled or
run on them. This helps increase the reliability and throughput of a cluster
by reducing preventable job failures due to misconfiguration, hardware failure, etc.
 * https://github.com/mej/nhc

## Bundle services data

Before we run all the bundles specified by `def.sara_services_enabled`. Will expand
all the unresolved variables for all bundles defined by `def.sara_services_enabled`.
So the order of defining the service bundles does not matter, eg:
  *  nhc
```#json
{
    "timeout": "$(sara_data.slurm[MessageTimeout])"
}
```

 * slurm
```#json
{
    "MessageTimeout": "20"
}
```
This would not expand because the `nhc` json data is read first and then `slurm`.

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

