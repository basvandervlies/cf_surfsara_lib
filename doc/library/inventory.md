# inventory.cf

Source: [inventory.cf](/masterfiles/lib/surfsara/inventory.cf)

You can control which inventory modules must be run and with optional arguments by
specifying the following variable in:
 * def.json
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
    "run_class": "debian",
    "run_bundle": "sara_dmidecode"
}
```

This statement determines which modules has to be run with optional arguments:
 * `args`: Arguments supplied to the module command (Optional)
 * `run_class`: Only run module if this class condition is met (Optional)
 * `run_bundle`: Run this bundle after a successful module execution (Optional)

The inventory bundle defines a directory variable for storing module data `sara_inventory.cache_dir`:
 * `/run/cf_inventory_cache`
 * can be overriden by `def.sara_inventory_cache_dir`

The module scripts must be located under `$(sys.workdir)/modules`. Only exit code '0' is considered
as promise kept all other exit codes are considered as promise failed. The class name is:
 * `canonify("sara_inventory_modules_<module_name>_failed")`

## lscpu

Is a wrapper script around the `lscpu` command. It converts the output to json and stores it in a json
file. If the file exists it will load the data and convert it to the json module data protocol. The
data can be accessed in cfengine:
 * `$(lscpu.data[Model_name])`
 * `"" usebundle => sara_show_data("lscpu", "data")`

Required argument: `"$(sara_inventory.cache_dir)"`

## dmidecode

Is a wrapper script around the `dmidecode` command. It converts the output to json and stores it in a json
file. If the file exists it will load the data and convert it to the json module data protocol. The
data can be accessed in cfengine:
 * `$(dmidecode.data[Base_Board_Information][0][Manufacturer])`
 * `"" usebundle => sara_show_data("dmidecode", "data")`

Required arguments: `"--output $(sara_inventory.cache_dir)/dmidecode.json --cf"`
