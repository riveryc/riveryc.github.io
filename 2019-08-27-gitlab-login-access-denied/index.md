# Gitlab Login Access Denied


Error message showing as below while trying to login to on prem gitlab:
!["gitlab access denied"](/img/post/20190827/gitlab_access_denied.png "gitlab access denied")

<!--more-->

## Background

- Gitlab is using LDAP (Microsoft Active Directory) to authenticate users.
- Confirmed with others users, only a small group of users are affected.
- Keep digging information by asking around... previously, users were in 2 different ldap schema, admin used `gitlab-rake gitlab:ldap:rename_provider[ldapbackup,ldapmain]` to merge all old users to a single auth provider. But tested yesterday, login was fine.

- - -

## Diagnostic

#### check log file: `/var/log/gitlab/gitlab-rail/production.log` while perform login action again...

```bash
tail -f /var/log/gitlab/gitlab-rail/production.log
```

Find following in the log file:

```bash
Processing by Ldap::OmniauthCallbacksController#ldapmain as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"[FILTERED]", "username"=>"riveryang", "password"=>"[FILTERED]"}
LDAP search error: No Such Object
LDAP search error: No Such Object
Could not update DN for riveryang (451)
error messages: {:extern_uid=>["has already been taken"], :user=>["has already been taken"]}
Redirected to https://gitlab.domain.works/users/sign_in
```

Seems like it cannot find my name `riveryang` by using DN, but when trying to update with new DN, error saying `extern_uid` and `user` has been taken

Searched around in Google, cannot find any useful information...

#### check in db (danger, please make sure you know what you are doing)

I don't know what's gitlab's DB looks like, so decided to look around. lol~

```psql
# Entering DB console
gitlab-rails dbconsole

# list current DB - psql cli command
gitlabhq_production=> \l
 gitlabhq_production | gitlab      | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 postgres            | gitlab-psql | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 template0           | gitlab-psql | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/"gitlab-psql"               +
                     |             |          |             |             | "gitlab-psql"=CTc/"gitlab-psql"
 template1           | gitlab-psql | UTF8     | en_US.UTF-8 | en_US.UTF-8 | "gitlab-psql"=CTc/"gitlab-psql"+
                     |             |          |             |             | =c/"gitlab-psql"

# switch databases
gitlabhq_production=> \c gitlabhq_production
psql (10.9, server 10.7)
You are now connected to database "gitlabhq_production" as user "gitlab".

# list all tables
gitlabhq_production=> \dt
 public | abuse_reports                                   | table | gitlab
 public | allowed_email_domains                           | table | gitlab
 public | analytics_cycle_analytics_group_stages          | table | gitlab
 public | analytics_cycle_analytics_project_stages        | table | gitlab
 public | appearances                                     | table | gitlab
 public | application_setting_terms                       | table | gitlab
 public | application_settings                            | table | gitlab
 public | approval_merge_request_rule_sources             | table | gitlab
 public | approval_merge_request_rules                    | table | gitlab
....
```

There is one table `users`, have a look what's inside:

```psql
gitlabhq_production=> select * from users;
  id  |                      email                      |                      encrypted_password                      | reset_password_token | reset_password_sent_at |    remember_created_at     | sign_in_count |     current_sign_in_at     |      last_sign_in_at       | current_sign_in_ip | last_sign_in_ip |         created_at         |         updated_at         |             name              | admin | projects_limit |            skype            |              linkedin               | twitter |                                                               bio                                                               | failed_attempts |         locked_at          |     username     | can_create_group | can_create_team |    state     | color_scheme_id |    password_expires_at     | created_by_id |  last_credential_check_at  |   avatar   |  confirmation_token  |        confirmed_at        |    confirmation_sent_at    |  unconfirmed_email  | hide_no_ssh_key |          website_url           | admin_email_unsubscribed_at |               notification_email                | hide_no_password | password_automatically_set |   location    |                     encrypted_otp_secret                     | encrypted_otp_secret_iv  | encrypted_otp_secret_salt | otp_required_for_login | otp_backup_codes |       public_email       | dashboard | project_view | consumed_timestep | layout | hide_project_limit | note |                           unlock_token                           | otp_grace_period_started_at | external |   incoming_email_token    |      organization      | auditor | require_two_factor_authentication_from_group | two_factor_grace_period | ghost | last_activity_on | notified_of_own_activity | preferred_language | email_opted_in | email_opted_in_ip | email_opted_in_source_id | email_opted_in_at | theme_id | accepted_term_id |      feed_token      | private_profile | roadmap_layout | include_private_contributions |       commit_email       | group_view | managing_group_id | bot_type
------+-------------------------------------------------+--------------------------------------------------------------+----------------------+------------------------+----------------------------+---------------+----------------------------+----------------------------+--------------------+-----------------+----------------------------+----------------------------+-------------------------------+-------+----------------+-----------------------------+-------------------------------------+---------+---------------------------------------------------------------------------------------------------------------------------------+-----------------+----------------------------+------------------+------------------+-----------------+--------------+-----------------+----------------------------+---------------+----------------------------+------------+----------------------+----------------------------+----------------------------+---------------------+-----------------+--------------------------------+-----------------------------+-------------------------------------------------+------------------+----------------------------+---------------+--------------------------------------------------------------+--------------------------+---------------------------+------------------------+------------------+--------------------------+-----------+--------------+-------------------+--------+--------------------+------+------------------------------------------------------------------+-----------------------------+----------+---------------------------+------------------------+---------+----------------------------------------------+-------------------------+-------+------------------+--------------------------+--------------------+----------------+-------------------+--------------------------+-------------------+----------+------------------+----------------------+-----------------+----------------+-------------------------------+--------------------------+------------+-------------------+----------
```

We can see all headers as object's properties above, but nothing mentioned about Ldap schema or otherthings.
Also listed few users's status by `select * from user where name like 'riveryang'`, nothing interesting or very useful, but I found my id is `792`...

As I got my user_id, check other tables `user_synced_attributes_metadata`:

```psql
gitlabhq_production=> select * from user_synced_attributes_metadata;
  id   | name_synced | email_synced | location_synced | user_id |  provider
-------+-------------+--------------+-----------------+---------+------------
    62 | f           | t            | f               |       4 |
    66 | f           | t            | f               |      15 |
  8335 | f           | t            | f               |     273 | ldapmain
 25250 | f           | t            | f               |     565 | ldapmain
 24843 | f           | t            | f               |     431 | ldapmain
 11527 | f           | t            | f               |     298 | ldapmain
 ...
```

Interesting, at least we got some mapping information between user_id and provider `ldapmain`, check if mine is mapped:

```psql
gitlabhq_production=> select * from user_synced_attributes_metadata where user_id = 792;
  id   | name_synced | email_synced | location_synced | user_id | provider
-------+-------------+--------------+-----------------+---------+----------
```

Uhmm, nothing... Probably that's why?

What if I manually add here? (I know I shouldn't do it, but just want to verify...)

```psql
gitlabhq_production=> insert into 
user_synced_attributes_metadata(name_synced, email_synced, location_synced, user_id, provider) 
values 
('f','t','f',792,'ldapmain');
```

Try again with web portal: same error... and try to do a search again 

```psql
gitlabhq_production=> select * from user_synced_attributes_metadata where user_id = 792;
  id   | name_synced | email_synced | location_synced | user_id | provider
-------+-------------+--------------+-----------------+---------+----------
```

IT'S GONE!!! So, it's dynamically changed by some logic behind sense... Well. OK... 

Keep digging... search for other tables that may relevent...

Found this `indentities`:

```psql
gitlabhq_production=> select * from identities;
  id  |                                           extern_uid                                           | provider | user_id |         created_at         |         updated_at         | secondary_extern_uid | saml_provider_id
------+------------------------------------------------------------------------------------------------+----------+---------+----------------------------+----------------------------+----------------------+------------------
  133 | cn=user1,dc=users,dc=domain,dc=local                                                           | ldapmain |     138 | 2017-12-04 08:26:32.378928 | 2017-12-04 08:26:32.378928 |                      |
  139 | cn=user2,dc=users,dc=domain,dc=local                                                           | ldapmain |     144 | 2017-12-11 05:39:45.465455 | 2017-12-11 05:39:45.465455 |                      |
  141 | cn=user3,dc=users,dc=domain,dc=local                                                           | ldapmain |     146 | 2017-12-11 12:39:24.546617 | 2017-12-11 12:39:24.546617 |                      |
   16 | cn=user4,ou=different OU,ou=Just Another OU,ou=all users,dc=domain,dc=local                    | ldapmain |      17 | 2016-10-24 09:15:17.363264 | 2019-04-15 01:30:03.462332 |                      |
  122 | cn=user5,ou=team Alpha,ou=OU_yeah,ou=all users,dc=domain,dc=local                              | ldapmain |     127 | 2017-11-21 03:27:38.129106 | 2018-12-08 11:55:52.766379 |                      |
```

What about myself: 

```psql
gitlabhq_production=> select * from identities where user_id = 792;
 id  |                            extern_uid                            | provider | user_id |         created_at         |         updated_at         | secondary_extern_uid | saml_provider_id
-----+------------------------------------------------------------------+----------+---------+----------------------------+----------------------------+----------------------+------------------
 955 | cn=riveryang,ou=devops,ou=diffou,ou=all users,dc=domain,dc=local | ldapmain |     792 | 2019-08-10 03:36:41.685002 | 2019-08-25 08:42:53.122385 |                      |
 807 | cn=riveryang,ou=devops,ou=fakeou,ou=all users,dc=domain,dc=local | ldapmain |     792 | 2016-12-10 03:36:41.685002 | 2019-07-15 02:53:33.121285 |                      |
(2 rows)
```

As above shown, one of them is previous DN that changed by HR (dont know why they change this...), anyway... it shouldn't be there... Let's get rid of it...

```psql
gitlabhq_production=> delete from identities where id = 955;
DELETE 1
```

Login to Gitlab again, Whooooya~

But I cannot just do this one by one only when user complaints... So proactively search:

```psql
gitlabhq_production=> select user_id, count(*)
from identities
group by user_id 
HAVING count(*)>1;
 user_id | count
---------+-------
      13 |     2
      27 |     2
      56 |     2
      97 |     2
     167 |     2
     171 |     2
     205 |     2
     207 |     2
     234 |     2
     237 |     2
     238 |     2
     244 |     2
     254 |     2
     301 |     2
     304 |     2
     307 |     2
     318 |     2
     339 |     2
     383 |     2
     401 |     2
     504 |     2
     516 |     2
     520 |     2
     533 |     2
     544 |     2
     554 |     2
     587 |     2
     610 |     2
     641 |     2
     670 |     2
     676 |     2
     685 |     2
     709 |     2
     719 |     2
     772 |     2
     774 |     2
     786 |     2
     803 |     2
     810 |     2
     850 |     2
     860 |     2
     891 |     2
(42 rows)
```

All above  `user_id` records are experiencing same issue, so here is the homework for you:

HOW TO USE SQL TO SMARTLY REMOVE DUPLICATE RECORDS IN ANOTHER TABLE... lol~~ 

Have fun

River
