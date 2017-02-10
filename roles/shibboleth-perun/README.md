Shibboleth-Perun
=========
**THIS ROLE IS NOT FUNCTIONAL AT THIS MOMENT!**
Installation of Shibboleth for Perun server.

After installation
------------------

 * If you wish to have SSO as default, move paths without authz info to fed version of LocationMatch tag (example with /pwd-reset/):
 	`# move "|/pwd-reset/" from CERT into FED version`
  	`<LocationMatch "^(/cert/pwd-reset/|/pwd-reset/)$">   ---- >   <LocationMatch "^(/cert/pwd-reset/)$">`
  	`<LocationMatch "^(/fed/pwd-reset/)$">                ---- >   <LocationMatch "^(/fed/pwd-reset/|/pwd-reset/)$">`
 
 	** Also change root redirect for GUI to:
 		`# make root redirect to fed-gui`
		`RewriteRule ^/$ /fed/gui/ [R]`

 * Now you need to contact federation operator or IdPs to setup the attribute filter in order to get SAML attributes about logged user.


Requirements
------------

Perun installed on your system (all required roles installed).

License
-------

BSD

Author Information
------------------

Jan Zvěřina, CESNET, zverina@cesnet.cz
