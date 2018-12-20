# def.cf

Source: [def.cf](/masterfiles/lib/surfsara/def.cf)

This is the standard `def.cf` used by cf_surfsara_lib. If your site has defined
their own then set the class `SURFSARA_SKIP_DEF_CF_INCLUDE` and this file will
not be included. The most important variable is:
 * `node_template_dir` and its default value is: `$(sys.workdir)/node_templates`

If you have your own `def.cf` then this variable **must** be set.

