This repository accompanies my answer to <https://serverfault.com/questions/1109463/apache2-openldap-authorization-fails-using-ldap-user/1109472>.

If you `docker-compose up` this environment, you'll get three Apache instances:

- On port 8081, using `httpd/mod_ldap.conf.v1`

  This is the original configuration and does not work.

- On port 8082, using `httpd/mod_ldap.conf.v2`

  This replaces `Require ldap-user` with `Require user` and works.

- On port 8083, using `httpd/mod_ldap.conf.v3`

  This does not use a provider alias and works.

To test these services, try to access the `/auth/` path with username `user1` and password `secret1`:

```
$ curl -u user1:secret http://localhost:8082/auth/
<!doctype html>
<html>
  <head>
    <title>LDAP Auth Example</title>
  </head>
  <body>
    # LDAP Auth Example

    Congratulations, you have successfully authenticated.
  </body>
</html>
```

You can see the configuration for a specific server at the `/config` path:

```
$ curl http://localhost:8082/config
#
# - Using a provider alias
# - Uses "Require user" instead of "Require ldap-user"

<AuthnProviderAlias ldap example>
  AuthLDAPURL ldap://ldap/ou=users,dc=example,dc=com?cn
  AuthLDAPBindDN uid=authreader,ou=system,dc=example,dc=com
  AuthLDAPBindPassword secret
</AuthnProviderAlias>

<Location "/auth">
  AuthName "LDAP"
  AuthType Basic
  AuthBasicProvider example
  Require user user1
</Location>

```
