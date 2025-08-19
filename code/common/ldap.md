## ldap验证连通性服务
ldapsearch -x -H "ldap://127.0.0.1:389" \
  -D "cn=admin,dc=io,dc=plus" \
  -w "admin123" \
  -b "dc=io,dc=plus" \
  "(objectClass=*)" dn

## ldap查看用户
ldapsearch -x -H "ldap://127.0.0.1:389" \
  -D "cn=admin,dc=io,dc=plus" \
  -w "admin123" \
  -b "ou=users,dc=io,dc=plus" \
  "(objectClass=person)" cn

openldap：
ldapsearch -x -D "cn=admin,dc=io,dc=plus" -w admin123 -b "dc=io,dc=plus" "(objectClass=*)"  #查看用户
ldapdelete -x -D "cn=admin,dc=io,dc=plus" -w admin123 "uid=csh,ou=users,dc=io,dc=plus"   #删除用户
