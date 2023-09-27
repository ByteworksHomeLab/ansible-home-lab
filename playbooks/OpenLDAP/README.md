# OpenLDAP

See https://galaxy.ansible.com/capitanh/openldap-ansible-role

See LDAP setup https://www.howtoforge.com/how-to-install-openldap-on-ubuntu-22-04/

```shell
ansible-galaxy install capitanh.openldap-ansible-role
```

# How to Run

Pass the admin password on the command line.

```shell
openldap_serverrootpw
```

```shell
ansible-playbook --ask-become-pass --extra-vars "ldap_bind_pw=[SECRET]" playbooks/OpenLDAP/main.yml
```
# Validating

```shell
sudo systemctl slapd status
```

## Firewall

Check the status.

```shell
sudo ufw status verbose
Status: inactive
```

```shell
sudo ufw enable
sudo ufw allow 389
sudo ufw allow 636
sudo ufw status verbose
```

```text
sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
53 (Bind9)                 ALLOW IN    Anywhere
22                         ALLOW IN    Anywhere
389                        ALLOW IN    Anywhere
636                        ALLOW IN    Anywhere
53 (Bind9 (v6))            ALLOW IN    Anywhere (v6)
22 (v6)                    ALLOW IN    Anywhere (v6)
389 (v6)                   ALLOW IN    Anywhere (v6)
636 (v6)                   ALLOW IN    Anywhere (v6)
```

Test the connection locally, then remotely.
```shell
ldapmodify -H ldapi:/// -D "cn=admin,dc=byteworksinc,dc=com" -W
```

# BOOTING CONFIG FOR LDIF

Generate a user password

```shell
slappasswd
New password:
Re-enter new password:
```

Restart the LDAP Config wizard.

```shell
sudo dpkg-reconfigure slapd

[sudo] password for ubuntu:
  Backing up /etc/ldap/slapd.d in /var/backups/slapd-2.5.16+dfsg-0ubuntu0.22.04.1... done.
  Moving old database directory to /var/backups:
  - directory unknown... done.
  Creating initial configuration... done.
  Creating LDAP directory... done.
```
Import the  desired ldif file.

```shell
ldapadd -x -D cn=admin,dc=byteworksinc,dc=com -W -f ldap.ldif
```

Verify the import.

```shell
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:///
dn: dc=byteworksinc,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: byteworksinc.com
dc: byteworksinc

dn: ou=People,dc=byteworksinc,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=byteworksinc,dc=com
objectClass: organizationalUnit
ou: Group

dn: cn=Admin,ou=Group,dc=byteworksinc,dc=com
objectClass: posixGroup
cn: Admins
cn: Admin
gidNumber: 5000
memberUid: smitchell

dn: cn=Developers,ou=Group,dc=byteworksinc,dc=com
objectClass: posixGroup
cn: Developers
gidNumber: 5001
memberUid: smitchell

dn: cn=smitchell,ou=Group,dc=byteworksinc,dc=com
cn: smitchell
objectClass: posixGroup
gidNumber: 10000
memberUid: smitchell

dn: uid=smitchell,ou=People,dc=byteworksinc,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: smitchell
sn: Steve
givenName: Mitchell
mail: smitchell@byteworksinc.com
cn: Steve Mitchell
displayName: Steve Mitchell
uidNumber: 10000
gidNumber: 10000
gecos: Steve Mitchell
loginShell: /bin/bash
homeDirectory: /home/smitchell
```

![1-network-parameters.png](images%2F1-network-parameters.png)

![2-authentication.png](images%2F2-authentication.png)

![3-BrowserOptions.png](images%2F3-BrowserOptions.png)

![4-LDAP-Tree.png](images%2F4-LDAP-Tree.png)
