# sara_user_consume_resources

Source: [sara_user_consume_resources.cf](/services/sara_user_consume_resources.cf)

This will terminate/kill processes that consume to much resources. The resources at this moment are:
 * exclude_owners: Is a list of process owners that are **NOT** killed
 * exclude_processes:   A regular expression of processes that are exclude from being killed
 * memory:  Kill process that use more memory then specified.
 * minutes:  Kill process that run longer then specified minutes

No template file are generated, we only need josn file(s) as input. The location of this
file(s) are:
 * templates/sara_user_consume_resources/json


## Usage

The bundle can be run via:
 * `def.scl_services_enabled`
```json
"vars": {
    "scl_services_enabled": [
            "...",
            "sara_user_consume_resources",
            "..."
    ]
}
```

The bundle will aways read the [default.json](/templates/sara_user_consume_resources/json/default.json) file
and extra json file(s) can be specified via:
 * def.cf
```
vars:
    any::
        "sara_user_consume_resources_json_files" slist => { "lisa.json" };
```

The variable must be `sara_user_consume_resources_json_files` and with this setup 1 extra json file will be  merged.



## DEBUG

If you want to debug this bundle set the `DEBUG_sara_user_consume_resources` class, eg:
 * `-DDEBUG_sara_user_consume_resources`

## Def usage

Here is an example to for a json file in  (def.cf):
```
vars:
    "sara_user_consume_resources_json_files" slist => { "lisa.json" ];
```

Same can also be set in `def.json`
```json
"vars": {
    "sara_user_consume_resources": {
        "json_files": [ "lisa.json" ]
    }
}
```

```json
"vars": {
    "sara_user_consume_resources": {
        "max_minutes": "20"
    }
}
```
