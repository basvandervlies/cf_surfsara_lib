# boot.cf

Source: [boot.cf](/masterfiles/lib/scl/boot.cf)


This will run all bundles that have the tag `scl_boot`.  This bundles are usually run at
boot time, eg install/ugrade the NVIDIA driver.  At SURF we use usally invoke `cf-agent` as
follows:
 * `cf-agent KI -DBOOT`

When this class is set we call this bundle.
