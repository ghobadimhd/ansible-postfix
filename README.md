Postfix
=========

Simple role for configuring postfix MTA server.

With this role you can configure postfix in sorts of ways: 

* simple sending reciving postfix mail server
* postfix with SASL authentication
* postfix with virtual domains and virtual mailboxes
* postfix configured to use dovecot authentication backend and delivery agent(lmtp)

## To Do

* test ldap maps and set proper default values
* add support for spamassassin
* add support for amavisd
* add support for more customizable restrication
* add support for postfix tunning

Requirements
------------

#### Ubuntu:
**The below requirements are needed on the host that executes this module.**
* python
* python-apt

Role Variables
--------------

**warrning:** ldap maps does not tested yet and default values of them is not valid. Be careful

|  name | default | description | acceptable values |
|---|---|---|---|
| mydomain | "{{ ansible_domain }}" | postfix mydomain parameter| |
| myhostname | "{{ ansible_fqdn }}" | postfix myhostname parameter. | |
| myorigin | "{{ myhostname }}" | postfix myorigin | |
| mydestination | "localhost 127.0.0.1 $myhostname $myhostname.$mydomain $myhostname.localdomain" | postfix mydestination | |
| relay_domains | "$mydestination" | postfix relay_domains | |
| mynetworks | "127.0.0.0/8 {% for address in ansible_all_ipv4_addresses %} {{ address }} {% endfor %}" | postfix mynetworks | |
| alias_file | /etc/aliases | postfix alias file path | |
| smtpd_helo_required | "yes" | postfix smtpd_helo_required | |
| smtpd_milters | inet:localhost:12301 | postfix smtpd_milters | |
| non_smtpd_milters | inet:localhost:12301 | postfix non_smtpd_milters | |
| submission_enable | false | if True submission port enabled in master.cf  | |
| recipient_restricts_enable | false | is True smtpd restriction applied | |
| smtpd_rbl_check_enable | false | if True rbl checks added to restriction | |
| rbl_hosts | [ zen.spamhaus.org] | list of rbl servers | |
| smtp_tls_enable | false | enable smtp tls (client side) | |
| smtp_tls_loglevel | 1 | log level  | |
| smtp_tls_cafile | /etc/ssl/certs/ca-certificates.crt (only work in ubuntu!) | ca file path. You should override this for centos | |
| smtpd_tls_enable | false | enable smtpd tls (server side) | |
| smtpd_tls_cert_file | /etc/ssl/certs/ssl-cert-snakeoil.pem (only work in ubuntu!) | TLS cert file path. You should override this for centos | |
| smtpd_tls_key_file | /etc/ssl/private/ssl-cert-snakeoil.key (only work in ubuntu!) | TLS key file path. You should override this for centos | |
| smtpd_tls_security_level | may | postfix smtpd_tls_security_level. should be one of "may,ecrypt,none" | |
| use_lmtp | false | use lmtp client to deliver mails to mailbox | |
| lmtp_path | lmtp:unix:private/dovecot-lmtp | path of lmtp socket | |
| mailbox_lmtp | yes | use lmtp client to deliver mails for local users | |
| virtual_lmtp | yes |  use lmtp client to deliver mails for virtual users | |
| use_lda | no | use external lda command for local delivery | |
| lda_command | '/usr/lib/dovecot/dovecot-lda -f "$SENDER" -a "$RECIPIENT"' | external lda command path and arguments. default is to use dovecot-lda | |
| postfix_sql_driver | mysql | postfix sql driver used for sql maps. (mysql, sqlite, posgres) | |
| postfix_sql_host | 127.0.0.1 | sql host address | |
| postfix_sql_database | vmail | database used in sql maps | |
| postfix_sql_user | postfix | user of sql maps  | |
| postfix_sql_password | postfix | database password of sql maps | |
| postfix_ldap_server_host | localhost | host address of ldap server used in ldap maps | |
| postfix_ldap_server_port | 389 | port of ldap server | |
| postfix_ldap_search_base | "ou=users,ou={{ ansible_hostname }},dc={{ ansible_domain }}" | ldap search base | |
| smtpd_sasl_enable | false | enable or disable stmpd SASL authentication | |
| smtpd_sasl_backend | pam | authentication backed. (pam, mysql, sasldb, ldap) | |
| smtpd_sasl_sql_select | select password from users where username = '%u' and domain = '%r' | query | |
| smtpd_sasl_ldap_uri | ldap://localhost | ldap host address for SASL authentication | |
| smtpd_sasl_ldap_user | postfix | SASL authentication ldap user | |
| smtpd_sasl_ldap_password | postfix | SASL authentication ldap password | |
| smtpd_sasl_ldap_mech | DIGEST-MD5 | SASL authentication ldap mech | |
| smtpd_sasl_dovecot_path | private/dovecot-auth | socket path auth for authenticating using dovecot | |
| vdomains_enable | false | enable virtual domains | |
| vdomains | [] | list of virtual domains that postfix accept mail for. this is only useful if maincf backend used for virtual domains | |
| vdomains_driver | hash | virtual domains backend driver | maincf, hash, sql, ldap |
| vdomains_sql_query | select domain from users where domain = '%s' | query used by sql driver. domain accepted by postfix if query match. but the content of query answer does not matter. see [sql_table](http://www.postfix.org/mysql_table.5.html) | |
| vdomains_ldap_search_base | "ou=users,ou={{ ansible_hostname }},dc={{ ansible_domain }}" | **NOT TESTED YET** search base of ldap query used by ldap driver of virtual domains | |
| vdomains_ldap_query_filter | domain=%s | **NOT TESTED YET** ldap query used by ldap driver of virtual domains | |
| vdomains_ldap_result_format | "%d" | **NOT TESTED YET** Format template  applied  to  result  attributes by ldap driver of virtual domains. usualy used to append or prepend tex to result. see [ldap_table](http://www.postfix.org/ldap_table.5.html) | |
| vdomains_ldap_result_attribute | NULL | **NOT TESTED YET** attribute searched by ldap driver as result of virtual domains. **FIXME** | |
| vmail_enable | false | enable virtual mailboxes | hash, sql, ldap |
| vmail_driver | hash | backend driver used for virual mailboxes | |
| vmail_base | /var/mail/virtual-mailboxes | virtual mailboxes location | |
| vmail_user | vmail | system user used for virtual mailboxes | |
| vmail_mysql_query | select mailbox from users where username = %u and domain = %d | query used by sql driver. mail accepted for user by postfix if query match. but the content of query answer does not matter. see [sql_table](http://www.postfix.org/mysql_table.5.html) | |
| vmail_ldap_search_base | "ou=users,ou={{ ansible_hostname }},dc={{ ansible_domain }}" | **NOT TESTED YET** | |
| vmail_ldap_query_filter | uid=%s | **NOT TESTED YET** | |
| vmail_ldap_result_format | "%d.%u" | **NOT TESTED YET** | |
| vmail_ldap_result_attribute | uid | **NOT TESTED YET** | |
| valias_domains_enable | false | enable virtual alias domains | |
| valias_domains_driver | hash | virtual alias domains backend driver | maincf, hash, sql, ldap |
| valias_domains | [] | list of virtual alias domains that postfix should accept. only useful if maincf driver used | |
| valias_domains_sql_query | select domain from users | query used by sql driver. mail accepted for domains by postfix if query match. but the content of query answer does not matter. see [sql_table](http://www.postfix.org/mysql_table.5.html)  | |
| valias_domains_ldap_search_base | "ou=users,ou={{ ansible_hostname }},dc={{ ansible_domain }}" | **NOT TESTED YET** | |
| valias_domains_ldap_query_filter | domain=%s | **NOT TESTED YET** | |
| valias_domains_ldap_result_format | "%d" | **NOT TESTED YET** | |
| valias_domains_ldap_result_attribute | uid | **NOT TESTED YET** | |
| valias_maps_enable | false | enable virtual alias maps | |
| valias_maps_driver | hash | backend driver of virtual alias maps | hash, sql, ldap |
| valias_maps_sql_query | select concat_ws('@', username, domain) as email from valias where alias_username = '%u' and alias_domain = '%d' | query used by sql driver. mail forwarded to address returned by query. see [sql_table](http://www.postfix.org/mysql_table.5.html) | |
| valias_maps_ldap_search_base | "ou=users,ou={{ ansible_hostname }},dc={{ ansible_domain }}" | **NOT TESTED YET** | |
| valias_maps_ldap_query_filter | domain=%s | **NOT TESTED YET** | |
| valias_maps_ldap_result_format | "%d" | **NOT TESTED YET** | |
| valias_maps_ldap_result_attribute | uid | **NOT TESTED YET** | |


Example Playbook
----------------

#### example 1:

```yaml
---
# simple postfix configuration
- hosts: mail
  roles:
    - role: ghobadimhd.postfix
      recipient_restricts_enable: false
```
#### example 2:

postfix with only SASL authentication and dovecot lmtp delivery.

default configuration use database named *vmail* that contains *users* table like below.

sql:
```sql
create database vmail ;
use vmail ;
create table if not exists users  (
  username nvarchar(80),
  domain nvarchar(80),
  password nvarchar(512),
);

```
playbook.yml:

```yaml
---
- hosts: mail
  roles:
    - role: ghobadimhd.postfix
      mydestination: ""
      postfix_sql_driver: mysql
      smtpd_sasl_enable: yes
      smtpd_sasl_backend: sql
      vdomains_enable: yes
      vdomains_driver: mysql
      use_lmtp: yes
```
License
-------

MIT

Author Information
------------------

Mohammad Ghobadi

Email: ghobadimhd@gmail.com
