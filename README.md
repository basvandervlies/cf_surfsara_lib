# SURFsara CFEngine Library (SCL) for mustache/json templates

At SURFsara we have developed a general library to generate files from templates. In our setup we can easily
specify the default values and override them in other json file(s) or via def.cf/json. The goal is to set
up an global  repository for mustache templates.

For all bundles the mustache/json file(s) will be copied to the local node directory (`$(def.node_template_dir)`:
 * The json and template file(s) are copied from the policy hub shortcut: `templates/$(bundle_name)`
 * The copies are placed in the local node directory: `$(def.node_template_dir)/$(bundle_name)`
 * The following json must always be present and will always be copied: *default.json*
 * Extra json file(s) can be specified in *def.cf/json*: `$(bundle_name)[json_files]`
 * Scripts can generate json file(s) on a host/node. The json file must be copied into:
    * `$(def.node_template_dir)/$(bundle_name)`
    * The generated file(s) are specified in def.cf/json: `$(bundle_name)[local_generated_json_files]`
 * You can override values via *def.json*, Note: This one always wins.
 * CFEngine variables are expanded.

Both senarios will be described in the subsection below. For both senarios you can specifiy multiple
json files. The files will be merged and the last one wins if the same variable name is used,eg:
 * a.json defines: `a : 1`
 * b.json defines: `a : 2`

If the order is `{ "b.json", "a.json" }` the value of *a* would be *1*

## Merge strategy

The merge strategy is::
  1. `default.json`
  1. `def.<bundle_name>[json_files]` if defined
  1. `def.<bundle_name>_json_files` if defined
  1. `def.<bundle_name>[local_generated_json_files]` if defined
  1. `def.<bundle_name>_local_generated_json_files` if defined
  1. `def.<bundle_name>` if defined in def.json or:
    * lib/surfsara/def.cf MPF setup
    * your own file with variable scope `def`

## Installation

there are two options
 * Include it in the Master Policy Framework (MPF)
 * Include it in your own framework


### def.node\_template\_dir

The  `def.node_template_dir` variable is set in `lib/surfsara/def.cf`, but can also be set
set in `def.json`. The *def.json* wins, eg:
```
vars:
{
   "node_template_dir" : "/etc/node_status/templates"
}
```

default value is: `/var/cfengine/surfsara_templates`

### CF-serverd shortcut configuration for cfengine version less then 3.10.1

For older versions you have to manually add the `shorcut templates` to `controls/cf_serverd.cf`
```
      "$(sys.workdir)/templates"
      handle => "server_access_grant_access_templates",
      shortcut => "templates",
      comment => "Grant access to templates directory",
      admit => { @(def.acl) };
```

### MPF installation

1. Login on your policy server.
1. `./mpf_installation`
1. Enable autorun, if you have not done it, by adding this class to your ```def.json``` file
```
{
   "classes" :
   {
    "services_autorun" : "any"
   }
}
```

You can test your installation with
 * `cf-agent -Kv | grep surfsara_autorun`

#### update ####

You can run the same script it will detect that its an update `mpf_installation`. This script will overwrite:
 * surfsara library files: `masterfiles/lib/surfsara`
 * surfsara services files: `masterfiles/services/surfsara`
 * mustache template files and default.json files: `/var/cfengine/templates`

### Own framework

1. Login on your policy server.
1. cp -a masterfiles/lib/surfsara `<masterfiles>/lib/surfsara`
1. cp -a examples/templates $(sys.workdir)/templates
1. include `/lib/surfsara/stdlib.cf` in your inputs
```
body common control
{
    inputs => {
        ...
        "lib/surfsara/stdlib.cf",
        ...
    };
}
```
See above to add `templates shortcut` to cf-serverd.

## Usage

There are several template setups for different services included with inline documentation. These setups are
used in production at SURFsara.
 1. services/apt.cf
 1. services/check_space.cf
 1. services/cron.cf
 1. services/dhclient.cf
 1. services/node_exporter.cf
 1. services/nscd.cf
 1. services/ntp.cf
 1. services/nvidia_gpu_prometheus_exporter.cf
 1. services/pam.cf
 1. services/pam_radius.cf
 1. services/postfix.cf
 1. services/resolv.cf
 1. services/sudo.cf
 1. services/sara_user_consume_resources.cf
 1. services/singularity.cf
 1. services/slurm_prometheus_exporter.cf
 1. services/ssh.cf
 1. services/systemd.cf
 1. services/tcpwrappers.cf
 1. services/tripwire.cf
 1. services/yum.cf

To enable the template on your system:
 * MPF:
  1. The prefered way is to use `def.sara_services_enabled` method in def.cf/def.json.
  1. Copy a setup to the `masterfiles/services/autorun` directory
 * Own Framework:
   * `def_sara_services_enabled` method
   * usebundle:
     1. ntp_autorun()
     1. tcpwrappers_autorun()

###  sara\_services\_enabled method

This is the prefered method for MPF and your own frameork. With this method you can contol which services are run
and which file are included, eg: def.json
```
"vars": {
    "sara_services_enabled": [
        "ntp",
        "resolv"
    ]
}
```
This will include the surfsara services file `ntp.cf` and `resolv.cf` and run all bundles that have the meta tag
`template_ntp` and `template_resolv`. The bundle run can be protected by an class statement, default is `any`, eg:
```
"ntp": {
    "run_class": "debian|centos"
    }
```

This will only run on  debian or centos hosts.

### def.json

In this file you can override settings for the templates. When the json data is merged. This one wins, eg:
```
"vars": {
    "ntp" : {
        "server": [ "<your_ip_server1>", "<your_ip_server2>" ]
    }
}
```

You can also specify json setup files:
```
"vars": {
    "tcpwrappers": {
        "json_files": [ "allow_ssh.json", "allow_http.json" ]
    }
}
```

#### Bundle classes

For every service you dynamically set classes in the bundle data, eg:
```
"vars": {
    "dhclient": {
       "classes": {
           "RESOLV_CONF": [ "r24n2" ]
       }
    }
}
```
This will set the class `DHCLIENT_RESOLV_CONF` on host/node `r24n2`

### lib/surfsara/def.cf


You can also override settings in this file, eg:
 * One variable:
```
vars:
    "ntp" data => parsejson( '{ "server" : [ "<your_ip_server1>" ] }' );
```
 * json file:
```
vars:
    "tcpwrappers" data => parsejson( '{ "json_files": [ "allow_ssh.json", "allow_http.json" ] '} );
```


If you defined your own `def.cf` and do not want the one included in this framework you can set the following class:
 * `SURFSARA_SKIP_DEF_CF_INCLUDE`


## cf-agent command line options

The SURFsara CFEngine library also checks for some classes:
 * To test with a local `templates` directory. This directory must be one level higher than your policy files directory (../templates):
  * `-DTEMPLATE_LOCAL_COPY`: Copy from local directory the mustache and json file(s).
  * `-DMUSTACHE_LOCAL_COPY`: Copy from local directory the mustache file(s)
  * `-DJSON_LOCAL_COPY`: Copy from local directory the json file(s)
 * To test local mustache/json changes in `$(def.node_template_dir)`, the copy of the json/mustache file(s) from the policy server can be skipped by:
  * `-DTEMPLATE_SKIP_COPY`: Skip copying of mustache and json files
  * `-DMUSTACHE_SKIP_COPY`:  Skip copying of the mustache files
  * `-DJSON_SKIP_COPY`: Skip copying of the json files
 * To debug the mustache setup: `-DDEBUG_MUSTACHE` (all service bundles)
 * To debug mustache for a service bundle, eg `-DDEBUG_ntp`

