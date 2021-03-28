<!-- vim-markdown-toc GFM -->

* [Version: 0.9.89 (2021-03-28)](#version-0989-2021-03-28)
* [Version: 0.9.88 (2021-03-16 Dre)](#version-0988-2021-03-16-dre)
* [Version: 0.9.82 (2020-12-05 sint)](#version-0982-2020-12-05-sint)
* [Version: 0.9.77 (2020-07-14 tommie)](#version-0977-2020-07-14-tommie)
    * [Tarball installation](#tarball-installation)
    * [Templates generation enhanceent](#templates-generation-enhanceent)
* [Version: 0.9.66 (2019-12-24)](#version-0966-2019-12-24)
    * [pkg\_management service](#pkg_management-service)
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
# Version: 0.9.89 (2021-03-28)

Fixed an installation error in `mpf_installation` script. The surfsara modules were copied to the
wrong directort was `/var/cfengine/modules` instead of `/var/cfengine/masterfiles/modules`

# Version: 0.9.88 (2021-03-16 Dre)
 * apache service changes:
    * Use `data` shortcut for copying files (is cfengine standard)
    * json data format for modules  has been changed for easy overriding configuration files
      *  added `modules_standard` and `modules_extra`.
    * Clean modules that are not defined in `modules_standard` and `modules_extra`
    * Clean up site files that are not used anymore
    * All apache directories are now standard variables, eg `apache.sites_dir` and can be specified via json
    * Added a new json variable `sites_generated` a list of files that are generated on the host, eg: jupyterhub
    * Added `copy_dirs` section for apache This allow to copy tomcat configuration file for workers
 * apt service changes:
    * Generate /etc/apt/auth.conf. This file is used for password protected repositories. 
 * pam service changes:
    *  Added genertation of /etc/security/limits.d/scl.conf when specified in json file (limits\_compute.json)
 * postfix service changes:
    *  Added new variable `smtpd_relay_restrictions` contolled via json variable `smtpd_relay_restrictions`
 * slurm service changes:
    * Added a new slurm configuration file: `acct_gather.conf` controlled with json variable `acct_gather_file`
    * Grouped accounting storage options in a section json variable `accounting_storage_section`
    * Added a new json variable `acct_gather_section`
    * Added a new plugin template `ear.mustache` (Energy  Aware Runtime)

Changed the meta tag for all services to `service_<name>` instead of `autorun`. The servicea can not be enabled via
the CFengine method it must use the methode defined in the library.  It only let to confusion so do not use the meta tag

# Version: 0.9.82 (2020-12-05 sint)
 * jupyterhub service chnages:
    * apache reverse proxy  bug fix do not double escape special chars
    * added announcement option, eg: maintenance announcement
    * perms can be set for etc\_dir and configuration files
    * added a restart schedule: `JUPYTERHUB_RESTART_SCHEDULE`
 * postfix service chnages:
    * Enable TLS when possible for postfix
 * slurm service chnages:
    * Tarball installations now support additional package installations
    * Added linkchilderen to create links `/usr/[s]bin` other programs expect this
    * Added some more config files to `slurm_mog_list`
    * Added `SLURM_FORCE_LINKS` class to recreate  links in `/usr/[s]bin`
    * spank\_plugins now supports `run_class` option.  It will only be installed if satisfied
    * tarball json file simplified
    * removed obsolete option: `CacheGroups`
    * symplified current version check for tarball installations
    * No sacctmgr dump file any more
    * copy `pam_slurm_adopt.so` if we install a new tarball
    * restart code for daemons is better
 * ssh service changes:
    * Moved `UsePrivilegeSeparation` to the DEPRICATED SECTION

# Version: 0.9.77 (2020-07-14 tommie)
 * Services added: jupyterhub, configurable\_http\_proxy.cf, enroot (nvidia container software), copy\_dirs
 * apache service changes:
    * Security enhancement: `TraceEnable off`
    * added `local.d` directory for files that are generated
 * apt service change:
    * added `--quiet` option to update to reduce the noise
 *  library files.cf added tarball installation
 * node\_exporter, nvidia\_gpu\_prometheus\_exporter change:
    * delete initrd file when systemd class is set
 * pam service change:
    * added new variable `pam.lib_dir`
 * pkg\_management service change:
    * pkg\_management service now support `run_class` option, eg:
```#json
    fail2ban: {
        action: purge,
        run_class: [ !LOGIN_NODE ],
        version: ""
    }
```
 * slurm service changes:
    * plugstack.conf is now also generated by mustache/json
    * Added default for gid/uid: 555
    * All dir variables are now set in a json file so we can support package/tarball installations
    * Added `disable_services` list variable. These servicea are disabled by the service bundle
    * pyxis.conf (enroot container slurm spank plugin) can be generated
    * systemd file can be generated for all services by setting class: `SLURM_SYSTEMD_SERVICES|SLURM_TARBALL`
    * Added SLURM tarball installation
    * Task plugin is configurable via json: `taskplugin_section`
    * DB purging can now be set via json: `purge_section`
 * sudo service change:
    * added new list variable for mustache: `runas_alias`
 * Templates generation enhancement when a data container is specified

## Tarball installation

Added a new installation method with the aid of copy\_files, namely:
 * `sara_service_install_tarballs(bundle_name)`

Here is an example for slurm (json file):
```#json
install_tarballs: [
    {
    check_dir: $(slurm.software_dir)/19.05.5,
    dest: $(slurm.tarball_dir)/slurm-19.05.5.tar.gz,
    extract: {
        cmd: $(paths.path[tar]) --extract --gzip --file,
        in_dir: $(slurm.software_dir)
    },
    mog: [ 0644, root, root],
    source: data/slurm/tarballs/$(sys.flavor)/slurm-19.05.5.tar.gz
    }
],
dir: /opt/slurm,
config_dir: $(sara_data.slurm[dir])/etc,
current_version: 19.05.5,                                                                                                                                                                        log_dir: /var/log/slurm,
opt_dir: /opt/slurm,
plugin_dir: $(sara_data.slurm[dir])/sw/current/lib/slurm,
plugstack_dir: $(sara_data.slurm[config_dir])/plugstack.conf.d,
scripts_dir: $(sara_data.slurm[opt_dir])/scripts,
software_dir: $(sara_data.slurm[dir])/sw,
spool_dir: /var/spool/slurm,
tarball_dir: $(sara_data.slurm[dir])/tarballs
```

This will extract in  `/opt/slurm/sw/19.05.5` and  create a soft link `current` to this version.

## Templates generation enhanceent

The bundle `sara_mustache_cf_data_2_file` can handle an option data parameter. This parameter
was constructed from:
 * `bundle_name[var_specified]`

This has been changed to:
 * `var_specified`

This gives a much greate flexibility which data is used in the mustache templates, eg: (yum)

old method: the data is constructed from bundle name and repository\_names
```
sara_mustache_cf_data_2_file("$(this.bundle)",
        "$(template_file)",
        "$(yum.repos_dir)/$(repository_names).repo",
        "$(repository_names))
```

new method: User just specify the data to be used
```
sara_mustache_cf_data_2_file("$(this.bundle)",
        "$(template_file)",
        "$(yum.repos_dir)/$(repository_names).repo",
        "sara_data.yum_repository[$(repository_names)])
```

# Version: 0.9.66 (2019-12-24)
 * Services added: apache2, chrony, pkg\_management
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

## pkg\_management service

With this service you can install/remove packages that are not handled by other services. Debian
alike systens have 2 more options:
 * purge: Purge the package + configuration files from the system
 * install-backports: Install package from debian backports repository.


You can priorize backports package above the stable one via the class `PRIO_BACKPORTS`. If this class
is set then the following file will be created with the aid of inline mustache:
  * `/etc/apt/preferences.d/99-surfsara` (overridable via json file)

The `backports` package will now be considered as `stable` package. The upgrade of a `backport`
package is the same as a `stable` package:
 * `apt  --simulate --ignore-hold upgrade`

example:
```#json
{
    "grep": {
        "action": "install_backports",
        "version": "latest"
    },
    "git": {
        "action": "install_backports",
        "version": "latest"
    }
}
```

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

