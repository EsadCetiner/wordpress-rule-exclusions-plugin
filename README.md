# OWASP CRS - WordPress Rule Exclusions Plugin
![Integration tests](https://github.com/coreruleset/wordpress-rule-exclusions-plugin/actions/workflows/integration.yml/badge.svg)

## Description

This plugin contains rule exclusions for [WordPress](https://wordpress.org/), a content management system (CMS), so it can be run flawlessly together with
OWASP CRS (CRS).

## Installation

For full and up to date instructions for the different available plugin
installation methods, refer to [How to Install a Plugin](https://coreruleset.org/docs/concepts/plugins/#how-to-install-a-plugin)
in the official CRS documentation.

## Testing

After the plugin is enabled, your WordPress instance should work without any
problems possibly caused by CRS (for example, false positives while blocking
requests). If you are still having any problems, please file a new issue on
[github](https://github.com/coreruleset/wordpress-rule-exclusions-plugin).

## Conditionally enabling the plugin for multi-application environments

If CRS is installed on an reverse-proxy or on an web server with multiple web applications,
then you may wish to only enable the WordPress plugin for WordPress only for enhanced security.
You can use the SecWebAppID variable to conditionally enable/disable this plugin. You set a value for
SecWebAppID per VirtualHosts, then you inspect the WebAppID variable as shown below to only 
enable the WordPress plugin for WordPress VirtualHosts.

See:

https://github.com/owasp-modsecurity/ModSecurity/wiki/Reference-Manual-(v3.x)#secwebappid

https://github.com/owasp-modsecurity/ModSecurity/wiki/Reference-Manual-(v2.x)#secwebappid

Add or uncomment the following rule in wordpress-rule-exclusions-config.conf:

```
SecRule &TX:wordpress-rule-exclusions-plugin_enabled "@eq 0" \
    "id:9507011,\
    phase:1,\
    pass,\
    nolog,\
    ver:'wordpress-rule-exclusions-plugin/1.0.0',\
    chain"
    SecRule WebAppID "!@streq wordpress" \
        "t:none,\
        setvar:'tx.wordpress-rule-exclusions-plugin_enabled=0'"
```

If you are using Coraza, then you can use the host header instead of SecWebAppID.
As of the writing of this example, Coraza doesn't support SecWebAppID.

See: https://coraza.io/docs/seclang/variables/#webappid

Add or uncomment the following rule in wordpress-rule-exclusions-config.conf:

```
SecRule &TX:wordpress-rule-exclusions-plugin_enabled "@eq 0" \
    "id:9507012,\
    phase:1,\
    pass,\
    nolog,\
    ver:'wordpress-rule-exclusions-plugin/1.0.0',\
    chain"
    SecRule REQUEST_HEADERS:Host "!@streq wordpress.example.com" \
        "t:none,\
        setvar:'tx.wordpress-rule-exclusions-plugin_enabled=0'"
```

## License

Copyright (c) 2024 OWASP CRS project. All rights reserved.

The OWASP CRS and its official plugins are distributed
under Apache Software License (ASL) version 2. Please see the enclosed LICENSE
file for full details.
