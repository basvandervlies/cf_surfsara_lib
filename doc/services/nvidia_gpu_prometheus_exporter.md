
# NVIDIA GPU PROMETHEUS EXPORTER

Source: [nvidia_gpu_prometheus_exporter.cf](/services/nvidia_gpu_prometheus_exporter.cf)

This bundle will generate these/this file(s) from mustache templates:

 * /etc/default/nvidia_gpu_prometheus_exporter

If one of the files is changed then the following ""class"" will be set:
 * scl_etc_default_nvidia_gpu_prometheus_exporter
 * scl_etc_systemd_system_nvidia_gpu_prometheus_exporter_service
 * scl_etc_init_d_nvidia_gpu_prometheus_exporter

These templates are located in:
 * templates/nvidia_gpu_prometheus_exporter
 * templates/nvidia_gpu_prometheus_exporter/json

The following json variables can be set in def.cf/json to invoke files bundles:
  * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

## USAGE

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "nvidia_gpu_prometheus_exporter",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/nvidia_gpu_prometheus_exporter/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "nvidia_gpu_prometheus_exporter_json_files" slist => { "sara.json" };
```

The variable must be ''nvidia_gpu_prometheus_exporter_json_files'' and with this setup 1 extra json file will be merged.

### DEBUG

if you want to debug this bundle set the `DEBUG_nvidia_gpu_prometheus_exporter` class, eg:
 * `DDEBUG_nvidia_gpu_prometheus_exporter`

## def.cf/json

See [default.json](/templates/nvidia_gpu_prometheus_exporter/json/default.json) what the default values are and
which variables can be overriden

### copy_dirs

When this variabele is set it will copy the directoy to the specified destination and can run a bundle
if there are changes, eg:
```
"copy_dirs": [
    {
        "dest": "$(scl.nvidia_gpu_prometheus_exporter[dir])",
        "exclude_dirs": [ ".git", ".svn" ],
        "purge": "true",
        "run_bundle": "nvidia_gpu_prometheus_exporter_restart",
        "source": "data/prometheus_exporters/nvidia_gpu_prometheus_exporter-1.0"
    }
]
```
