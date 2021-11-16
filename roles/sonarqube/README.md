# SonarQube Server

Ansible role to provision a standalone SonarQube server. Please be aware that 
the following collection is in an alpha state and may not work as expected.

This role also assumes you have postgres installed onto the target machine and
the postgres daemon is running. If you require a collection to provision a 
postgres service, checkout my postgres collection. 

## Variables

| Name | Description | Default |
| ---- | ------------| --------|
| os_arch | Target architecture | x64 |
| locale | Target locale | en_GB.UTF-8 |
| temurin_version | Temurin (AdoptOpenJDK) Version | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>major</td><td>Temurin major version.</td><td>11</td></tr><tr><td>minor</td><td>Temurin minor version.</td><td>0</td></tr><tr><td>patch</td><td>Temurin patch version.</td><td>13</td></tr><tr><td>patch</td><td>Temurin build number.</td><td>8</td></tr></tbody></table> |
| sonarqube_installation_directory | Target installation directory | /opt |
| sonarqube_web_javaopts | Launch java opts | -Xmx512m -Xms128m -XX:+HeapDumpOnOutOfMemoryError |
| sonarqube_web_host | Host address to listen for web connections | 0.0.0.0 |
| sonarqube_web_context | Web context. Blank means SonarQube will serve up content using the root "/" context. | "" |
| sonarqube_web_port | Port to listen for web connections | 9000 |
| sonarqube_auth_jwt_secret | The JWT secret was generated using the following command </p><code>echo -n "superl33tp455w0rd" &#124; openssl dgst -sha256 -hmac "key" -binary &#124; base64</code></p>This should be updated if you intend on using this role for production. | 9peL46dMerbJ04McS58bzJ0ybK5rMwCiUu+MzPnRIyw= |
| sonarqube_system_user | The system user that will run the SonarQube service | sonar
| sonarqube_version | The version of SonarQube to install | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>major</td><td>SonarQube major version.</td><td>9/td></tr><tr><td>minor</td><td>SonarQube minor version.</td><td>1</td></tr><tr><td>patch</td><td>SonarQube patch version.</td><td>1</td></tr><tr><td>patch</td><td>SonarQube build number.</td><td>47736</td></tr></tbody></table> |
| database.sonarqube | The database that will be created for this installation | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>host</td><td>The database host to connect to.</td><td>127.0.0.1/td></tr><tr><td>name</td><td>The name of the database to create.</td><td>sonar</td></tr><tr><td>username</td><td>Name of the user that will be used to access the database.</td><td>sonar</td></tr><tr><td>password</td><td>the user's password.</td><td>password</td></tr><tr><td>enabled</td><td>Should the database be created?</td><td>true</td></tr></tbody></table> |
| sonarqube_sso_enabled | Set sso authentication status. | false |
| sonarqube_sso_headers | Headers | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>login</td><td>Name of the header to get the user login.</td><td>X-Forwarded-Login/td></tr><tr><td>name</td><td>Name of the header to get the user name.</td><td>X-Forwarded-Name</td></tr><tr><td>email</td><td>Name of the header to get the user email.</td><td>X-Forwarded-Email</td></tr><tr><td>groups</td><td>Name of the header to get the list of user groups, separated by comma.</br>If the sonar.sso.groupsHeader is set, the user will belong to those groups if groups exist in SonarQube.</br>If none of the provided groups exists in SonarQube, the user will only belong to the default group.</br>Note that the default group will always be set.</td><td>X-Forwarded-Groups</td></tr></tbody></table> |
| sonarqube_sso_refresh_interval | Interval used to know when to refresh name, email and groups. | 5 |
| sonarqube_ldap_enabled | Enable LDAP Authentication. | false |
| sonarqube_auth_downcase | Set to true when connecting to a LDAP server using a case-insensitive setup. | false |
| sonarqube_ldap_host | The Host/URL of the ldap service | 127.0.0.1 |
| sonarqube_ldap_port| The port that will be used to connect to the ldap service | 389 |
| sonarqube_ldap_binddn | The username of an LDAP user to connect (or bind) with. Leave this blank for anonymous access to the LDAP directory. | "" |
| sonarqube_ldap_bind_password | The password of the user to connect with. Leave this blank for anonymous access to the LDAP directory. | "" |
| sonarqube_ldap_auth_method | Set the authentication method. Possible values: simple, CRAM-MD5, DIGEST-MD5, GSSAPI. See [the tutorial on authentication mechanisms](http://java.sun.com/products/jndi/tutorial/ldap/security/auth.html) | simple |
| sonarqube_ldap_realm | See [Digest-MD5 Authentication](http://java.sun.com/products/jndi/tutorial/ldap/security/digest.html), [CRAM-MD5 Authentication](http://java.sun.com/products/jndi/tutorial/ldap/security/crammd5.html) | example.local |
| sonarqube_ldap_cxfactory | Context factory class. | "" |
| sonarqube_ldap_starttls | Enable use of StartTLS. | false |
| sonarqube_ldap_follow_referrals | Follow referrals or not. See [Referrals in the JNDI](http://docs.oracle.com/javase/jndi/tutorial/ldap/referral/jndi.html) | true |
| sonarqube_ldap_user_mapping | User Mapping. | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>basedn</td><td>Distinguished Name (DN) of the root node in LDAP from which to search for users.</td><td>cn=users,dc=example,dc=local</td></tr><tr><td>request</td><td>LDAP user request.</td><td>(&(objectClass=inetOrgPerson)(uid={login}))</td></tr><tr><td>real_name</td><td>Attribute in LDAP defining the user’s real name.</td><td>cn</td></tr><tr><td>email</td><td>Attribute in LDAP defining the user’s email.</td><td>mail</td></tr></tbody></table> |
| sonarqube_ldap_group_mapping | Group Mapping. | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>basedn</td><td>Distinguished Name (DN) of the root node in LDAP from which to search for groups.</td><td>cn=groups,dc=example,dc=local</td></tr><tr><td>request</td><td>LDAP group request.</td><td>(&(objectClass=groupOfUniqueNames)(uniqueMember={dn}))</td></tr><tr><td>id</td><td>Property used to specify the attribute to be used for returning the list of user groups in the compatibility mode.</td><td>cn</td></tr></tbody></table> |
| sonarqube_ce_javaopts | Compute Engine Java opts. | -Xmx512m -Xms128m -XX:+HeapDumpOnOutOfMemoryError -server |
| sonarqube_elasticsearch_javaopts | Elasticsearch Java opts. | -Xmx512m -Xms512m -XX:MaxDirectMemorySize=256m -XX:+HeapDumpOnOutOfMemoryError |
| sonarqube_elasticsearch_port | Elasticsearch Listening port. | 9001 |
| sonarqube_update_center | SonarQube Update Center Configuration. | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>enabled</td><td>Set whether to enable the update center.</td><td>false</td></tr><tr><td>proxy_enabled</td><td>Set whether to use a proxy for connecting to the update center.</td><td>false</td></tr><tr><td>proxy_config</td><td>A set of key/value pairs that hold the proxy host/port information.</td><td>example: </p>http.proxyHost: 127.0.0.1</br>http.proxyPort: 5612</td></tr></tbody></table> |
| sonarqube_log_path | Path to store logs. | logs |
| sonarqube_log_rolling_policy | Rolling policy of log files </br><ul></t><li>based on time if value starts with "time:", for example by day ("time:yyyy-MM-dd") or by month ("time:yyyy-MM")</li></br></t><li>based on size if value starts with "size:", for example "size:10MB"</li></br></t><li>disabled if value is "none". That needs logs to be managed by an external system like logrotate.</li> | time:yyyy-MM-dd |
| sonarqube_log_max_files | Maximum number of files to keep if a rolling policy is enabled. Set to 0 to disable. | 7 |
| sonarqube_log_web_access | Log web activity. | true |
| sonarqube_enable_telemetry | Share anonymous statistics with SonarQube | false |
| sonarqube_loglevel | Set the loglevel for each component | <table><thead><tr><th>Name</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td>global</td><td>Set the global loglevel.</td><td>INFO</td></tr><tr><td>app</td><td>Set the loglevel for the Main process of SonarQube (aka WrapperSimpleApp, the bootstrapper process starting the 3 others).</td><td>INFO</td></tr><tr><td>web</td><td>Set the loglevel for the webserver.</td><td>INFO</td></tr><tr><td>ce</td><td>Set the loglevel for the Compute Engine Service.</td><td>INFO</td></tr><tr><td>es</td><td>Set the loglevel for the Elasticsearch service.</td><td>INFO</td></tr></tbody></table> |