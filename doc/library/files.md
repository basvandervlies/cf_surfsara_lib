# files.cf

Source: [files.cf](/masterfiles/lib/scl/files.cf)

For copying files/dirs in the service bundle from the policy server we have defined 2 bundles:
 1. sara_service_copy_dirs
 1. sara_service_copy_files

## sara_service_copy_dirs(bundle_name)

This bundle will read the json attribute `copy_dirs` from the specified `bundle_name`. The following json
attribute can be specified:
 * compare: which mode must we use to check the difference, default: digest
 * dest: destination directory
 * exclude_dirs:  exclude this dirs from copying, default: [ ".git", ".svn" ]
 * mog: mode/owner/group  of all files/directories, default not set
 * preserve: preserve same mode as source directory, default: false
 * purge: exact copy of the source directory, default: false
 * run_bundle: The name of the bundle to run when something is repaired, default not set.
 * source: source directory
 * type_check:  Check if destination adn source are of the same type, default: false

The  copy attributes can be overriden or specified  by json data, eg:
```json
"slurm": {
    "copy_dirs": [
        {
            "dest": "$(sara_data.slurm[scripts_dir])",
            "exclude_dirs": [ ".git", ".svn" ],
            "mog": [ "0700", "root", "root" ],
            "purge": "true",
            "source": "cf_bundles_dir/slurm/scripts"
        }
    ]
}
```
## sara_service_copy_files(bundle_name)

This bundle will read the json attribute `copy_files` from the specified `bundle_name`. The following json
attribute can be specified:
 * dest: destination
 * mog: mode/owner/group of the file
 * run_bundle: The name of the bundle to run when something is repaired, default not set.
 * secure_cp from mpf is used to copy the files
 * source: source

The `copy_files` can specified by json data eg:
```json
"slurm": {
    "copy_files": [
        {
            "dest": "$(slurm.config_dir)/job_submit.lua",
            "source": "cf_bundles_dir/slurm/lisa/job_submit.lua",
            "mog": ["0644", "slurm", "slurm"]
        }
    ]
}
```

## sara_service_install_tarballs(bundle_name)

This bundle will read the json attribute `install_tarballs` from the specified `bundle_name`. The following json
attribute can be specified:
 * check_dir:  CHeck if the directory exists. If not extract the tarball
 * dest:  destination
 * extract:  which `cmd` to use to extract the tarball and `in_dir` which dir to extract
 * mog: mode/owner/group of the file
 * secure_cp from mpf is used to copy the files
 * source: source

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
current_version: 19.05.5,,
software_dir: $(sara_data.slurm[dir])/sw,
tarball_dir: $(sara_data.slurm[dir])/tarballs
```

## sara_make_cron_file(name. data)

This bundle install system cronjob in `/etc/cron.d`. The bundle is called with:
 * sara_make_cron_file(name, data)

Where:
 * name: is the name of the cronjob file
 * data: Json data. eg: 2 minute cronjob
```json
{
    "minute": "*/2",
    "hour": "*",
    "day_of_month": "*",
    "month": "*",
    "day_of_week": "*",
    "user": "root",
    "command": "/bin/ls"
}
```
