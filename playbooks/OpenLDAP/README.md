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


```shell
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:///

dn: dc=byteworksinc,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: byteworksinc.com
dc: byteworksinc
```

## Password problems

```shell
sudo slappasswd
New password:
Re-enter new password:

/etc/ldap/slapd.d# ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b  cn=config olcRootDN=cn=admin,dc=byteworksinc dn olcRootDN olcRootPW
```

```shell
# ldapsearch -x -b "dc=byteworksinc,dc=com" -H ldap://192.168.3.2 cn

ldapsearch -x -b "dc=byteworksinc,dc=com" -H ldap://127.0.0.1 cn

# extended LDIF
#
# LDAPv3
# base <dc=byteworksinc,dc=com> with scope subtree
# filter: (objectclass=*)
# requesting: cn
#

# byteworksinc.com
dn: dc=byteworksinc,dc=com

# groups, byteworksinc.com
dn: ou=groups,dc=byteworksinc,dc=com

# people, byteworksinc.com
dn: ou=people,dc=byteworksinc,dc=com

# stevemitchell, people, byteworksinc.com
dn: uid=stevemitchell,ou=people,dc=byteworksinc,dc=com
cn: Steve Mitchell

# admin, groups, byteworksinc.com
dn: cn=admin,ou=groups,dc=byteworksinc,dc=com
cn: admin

# search result
search: 2
result: 0 Success

# numResponses: 6
# numEntries: 5
```

Test Admin Sign in

```shell
ldapsearch -x -b "dc=byteworksinc,dc=com" -H ldap://127.0.0.1  -D "cn=admin,ou=people,dc=byteworksinc,dc=com" -W 
```

![1-network-parameters.png](images%2F1-network-parameters.png)

![2-authentication.png](images%2F2-authentication.png)

![3-BrowserOptions.png](images%2F3-BrowserOptions.png)

![4-LDAP-Tree.png](images%2F4-LDAP-Tree.png)
