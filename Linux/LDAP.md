Search user:
```
ldapsearch -b "OU=Users,OU=org,DC=domain,DC=local" -H ldap://<ip>:<port> -D "CN=User,OU=RTDC,OU=Users,OU=org,DC=domain,DC=local" -W "(&(objectClass=person)(objectClass=user)(sAMAccountName=some_user))"
```
Search group:
```
ldapsearch -b "OU=Groups,OU=org,DC=domain,DC=local" -H ldap://<ip>:<port> -D "CN=User,OU=RTDC,OU=Users,OU=org,DC=domain,DC=local" -W "(&(objectClass=group)(cn=some_group))"
```