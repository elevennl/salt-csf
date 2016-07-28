# salt-csf
A utility that allows you to manage CSF Firewall

## Configuration Levels
 * This formula has multiple configuration levels to allow for certain configuration to be set for all servers.
 * Defaults (`defaults.yaml`) < common settings (`csf['host']['common']`) < host specific settings (`csf['host'][grains['id']]`)
 * An value with a higher level will override a configuration with a lower level. For example, if `csf['host']['common']['config']['main']['TESTING']` is defined, and `csf['host'][grains['id']]['config']['main']['TESTING']` is defined, the value of `csf['host'][grains['id']]['config']['main']['TESTING']` will be used.

## Configuration
### csf.conf
 * Configuration inside `csf.conf` can be changed by defining keys and values inside the `csf['host'][grains['id']]['config']['main']` dict. For example, `csf['host'][grains['id']]['config']['main']['TESTING']` configures the value of `TESTING` in `csf.conf`
 * All keys/values will be default after installing CSF for the first time, so it may be a good idea to set keys such as `TCP_IN` and `UDP_IN` by default as well as disabling TESTING mode.
### Additional Configuration Files
 * The contents of remaining configuration files (csf.redirect, csf.blocklists, etc), can be defined using a list. For example, the list at `csf['host'][grains['id']]['config']['redirect']` sets the contents of csf.redirect
 * By default, no files will be modified until the list is defined
 * To be future proof, simply add an additional file in csf/config to define new configuration files. See other files in csf/config for the template format.
 * See pillar.example for examples

## Firewall Rules (csfpre.sh/csfpost.sh)
 * Configured using Rule Groups or manual rules
 * `csfpre.sh` rules are configured in `csf['host'][grains['id']]['rule']['pre']` while `csfpost.sh` rules are configured in `csf['host'][grains['id']]['rule']['post']`
 * Manual rules are added in the form of a list in `csf['host'][grains['id']]['rule']['pre']['contents']` or `csf['host'][grains['id']]['rule']['post']['contents']`
 * Rule Groups are defined in `csf['rule']['_rulename_']` with `['_rulename_']` being the name of the rule group
 * Rule Groups can be added to `csfpre.sh` or `csfpost.sh` by adding the `['_rulename_']` to the list at `csf['host'][grains['id']]['rule']['pre']['groups']` or `csf['host'][grains['id']]['rule']['post']['groups']`.
 * Rule Groups are added before manual rules.

## csf.deny
 * csf.deny is used by LFD to block IPs that have failed to login
 * If you manage csf.deny by defining csf['host']['common']['config']['deny'] or csf['host']['grains['id']]['config']['deny'], you will stop LFD from functioning properly as the IPs that LFD has written to the file will be removed each time the CSF state is run

## Notes
 * LFD will not start if sendmail or other applications are missing, if you see the lfd service attempt to start each time the CSF state is run, check the LFD logs

## Supported OSes
 * Ubuntu >= 12.04
 * Debian oldstable/stable/unstable
 * Centos 6/7
