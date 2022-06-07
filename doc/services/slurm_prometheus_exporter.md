# SLURM_PROMETHEUS_EXPORTER

**This bundle must be rewritten to the new standard**

Source: [slurm_prometheus_exporter.cf](/services/slurm_prometheus_exporter.cf)

This bundle will generate these/this file(s) from mustache templates:
 * /etc/default/slurm_prometheus_exporter

If one of the files is changed then the following ""class"" will be set:
 * scl_etc_default_slurm_prometheus_exporter

These templates are located in:
 * templates/slurm_prometheus_exporter
 * templates/slurm_prometheus_exporter/json

The following json variables can be set in def.cf/json to invoke files bundles:
  * copy_dirs: See [files.cf](/masterfiles/lib/scl/files.cf)

