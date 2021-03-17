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
