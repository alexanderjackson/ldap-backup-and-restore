**Preface**

These two scripts are currently beeing used to backup and restore the LDAP tree of an Zarafa Server. Skripts have been tested to work with Debian Wheezy (64bit) and OpenLDAP supporting live configuration.



**Quick install**

wget https://raw.githubusercontent.com/alexanderjackson/ldap-backup-and-restore/master/ldap-backup -O /usr/local/sbin/ldap-backup
wget https://raw.githubusercontent.com/alexanderjackson/ldap-backup-and-restore/master/ldap-restore -O /usr/local/sbin/ldap-restore
chown root.root /usr/local/sbin/ldap-backup /usr/local/sbin/ldap-restore
chmod 500 /usr/local/sbin/ldap-backup /usr/local/sbin/ldap-restore


**Installation**

* Copy files to scripts to /usr/local/sbin/
* chown root.root /usr/local/sbin/ldap-backup /usr/local/sbin/ldap-restore
* chmod 500 /usr/local/sbin/ldap-backup /usr/local/sbin/ldap-restore


**Create backup with ldap-backup**

Just run ldap-backup with root privileges.



**This will get backed**

* LDAP config data (slapcat -n 0)
* LDAP DIT data (slapcat -n 1) 
* LDAP accesslog (slapcat -n 2) - Disabled by default
* Folders: /etc/ldap and /var/lib/ldap

Backups will get saved to /var/backups/ldap-$(date +%Y%m%d-%H%M%S)



**Restore backup with ldap-restore**

Just run ldap-restore with root priviliges but make sure you have a full backup before attempting to restore. Backing up with ldap-backup might not be sufficient enough.



**How ldap-restore it works**

* ldap-restore will show 7 existing backups to restore from. More Backups might be available at /var/lib/ldap-backup-$(date +%Y%m%d-%H%M%S)...
* By default the latest backup will be preselected for restoration 
* You can overide the default selection by entering a different backup folder
* Accept restoration with [y] or abort with [n]
* The script will call ldap-backup before actually proceeding with the restoration
* slapd will get stopped
* The folder /var/lib/ldap will get copied to /var/lib/ldap-backup-$(date +%Y%m%d-%H%M%S)
* The content of /var/lib/ldap/ will get deleted
* config.ldif and domain.ldif from backup folder will get imported with slapadd
* File ownership (openldap:openldap) on /var/lib/ldap/ and /etc/ldap/slapd.d/ will be preserved
* slapd will get started

If for whatever reason the restore attempt with ldap-restore fails, you can manually try to restore your LDAP server from the *.tgz packages within the backup folders (/var/lib/ldap-backup-$(date +%Y%m%d-%H%M%S)) created by ldap-backup. The *.tgz files are created by ldap-backup but are not used by ldap-restore. ldap-restore only tries to restore by reimporting config.ldif and domain.ldif. The *.tgz files are intended as a last resort if something goes wrong. Not sure if they really include everything for a desaster recovery. Also the folders where backed up while LDAP was actually running. Could this produce faulty backups?
