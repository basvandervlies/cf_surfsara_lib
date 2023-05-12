# template.cf

Source: [templates.cf](/masterfiles/lib/scl/templates.cf)

Here all bundles are listed that are involved in parsing json data and
generating the templates files.

## scl(bundle_name, json_files)

This bundle will merge the json files into a data container which has the same name
as the given `bundle_name`. This data container can be accessed by other bundles
as:
 * `scl.<bundle_name>.<variable_name>`

NOTE: cfengine variables that are definied in the json files, eg: `$(def.hostname)`
are expanded.

The json files are read from the following local directory on the host:
 * `$(def.node_template_dir)/$(bundle_name)`

There is one exception if the bundle name is `expand_all_service_data`. Then the
bundle will loop over the list `def.scl_services_enabled` to expand cfengine variables.
Now service bundles can can reference variables of other service bundles,eg
```json
{
 "nhc": {
     "timeout": "$(scl.slurm[MessageTimeout])"
 },
 "slurm": {
    "MessageTimeout": "20"
 }
}
```
## scl_data_autorun(bundle_name)

This bundle wil take care of copying the json files from the policy hub to:
 * $(def.node_status_dir)/$(bundle_name)/json

After the copying it will merge all json files and the parsed data is available as:
 * cfengine: `$(scl.<bundle_name>[<var>])`
 * mustache: `vars.scl.<bundle_mame>.<var>`

After merging it will determine if we have to set cfengine classes based on the json variable
`classes` defined in the bundle json data.
## scl_json_classes_2_cfengine(bundle_name)

This bundle is internally and will set cfengine classes based on the json `classes`
variable defined in the bundle json data, eg:
```json
"dhclient": {
    "classes": {
        "RESOLV_CONF": "any"
    }
}
```

This will set the class: `DHCLIENT_RESOLV_CONF` for class expression `any`.
all classes wil be prefix by the `bundle_name` in uppercase.
## scl_json_copy(bundle_name, json_files)

The bundle is internally and will take care of copying the json file(s) from the bundle data directory
on the policy hub to a the local node directory.
## scl_json_copy_and_merge(bundle_name)

It use the same setup as above. So the variables must be defined in:
 * `def.$(bundle_name)_json_files` (for global definitions)
 * `def.$(bundle_name)[json_files]`(used to override/extend the global ones)
 * `def.$(bundle_name)_local_generated_json_files` (for global definitions)
 * `def.$(bundle_name)[local_generated_json_files]` (used to override/extend the global ones)

This bundle will copy `def.$(bundle_name)_json_files` data files to `$(def.node_template_dir)/$(bundle_name)` directory.
It uses the following bundles:
 * `bundle agent scl_json_copy`
 * `bundle agent scl`

The bundle is called by:
 * scl_data_autorun or scl_mustache_autorun
## scl_mustache_autorun(bundle_name)

This bundle will take of copying the mustache template file(s) and expanding the template(s)
## scl_mustache_service_autorun(bundle_name)

This bundle will take of copying the mustache template file(s) and expanding the template(s). The
parameter required is the `service_name` and optional we can specify form which bundle we need
to get the `template_2_destination` json definition from, eg:
 * `scl_mustache_service_autorun("resolv", "")`, uses `resolv.template_2_destination`
 * `scl_mustache_service_autorun("resolv", "resolv_define")`, uses `resolv_define.template_2_destination`
## scl_mustache_copy(bundle_name, files)

The bundle is internally and will take care of copying the template file(s) from the bundle data directory
on the policy hub to a the local node directory. The bundle is called by:
 * scl_mustache_autorun
## scl_mustache_expand(bundle_name)

This bundle is used internally and will expand the mustache template file(s) with the json data. The
bundle is called by:
 * scl_mustache_autorun
 * scl_mustache_service_autorun
## scl_mustache_cf_data_2_file(service_name, template_file, destination , data_section)

With this bundle you can generate a file from a template with cfengine internal json data. There are 2
options:
 1. json data section variable passed as argument. Then the section will be merged from the
    cfengine internal json data as toplevel. So variables in mustache file must be referenced
    without the service name, eg `<variable_name>` instead of `vars.scl.$(service_name).<variable_name>`
 1. cfengine internal json data if you do not pass your own json data section variable

The template is fetch from the local node direcrory:
 * `$(def.node_template_dir)/$(service_name)/$(template_file)`

When json data section variable is specified. You must specify which date section you want to use. The json
files must be merged via scl, as we only can merge data from this bundle, eg:
 * `scl.<service_name>[$(data_section)]`

This data will then be used as toplevel for the mustache template. You can just use the variables
name(s): `<variable_name>`
