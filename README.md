# nfrastack/container-nextcloud

## About

This repository will build a conatiner image for running [Nextcloud](https://nextcloud.com/) - A groupware platform.

## Maintainer

* [Nfrastack](https://www.nfrastack.com)

## Table of Contents

- [About](#about)
- [Maintainer](#maintainer)
- [Table of Contents](#table-of-contents)
- [Prerequisites and Assumptions](#prerequisites-and-assumptions)
- [Installation](#installation)
  - [Prebuilt Images](#prebuilt-images)
    - [Multi-Architecture Support](#multi-architecture-support)
  - [Quick Start](#quick-start)
  - [Persistent Storage](#persistent-storage)
  - [Environment Variables](#environment-variables)
    - [Base Images used](#base-images-used)
- [Maintenance](#maintenance)
  - [Shell Access](#shell-access)
- [Support & Maintenance](#support--maintenance)
- [References](#references)
- [License](#license)

# Prerequisites and Assumptions
*  Assumes you are using some sort of SSL terminating reverse proxy such as:
   *  [Traefik](https://github.com/nfrastack/container-traefik)
   *  [Nginx](https://github.com/jc21/nginx-proxy-manager)
   *  [Caddy](https://github.com/caddyserver/caddy)

## Installation

### Prebuilt Images

Feature limited builds of the image are available on the [Github Container Registry](https://github.com/nfrastack/container-nextcloud/pkgs/container/container-nextcloud) and [Docker Hub](https://hub.docker.com/r/nfrastack/nextcloud).

To unlock advanced features, one must provide a code to be able to change specific environment variables from defaults. Support the development to gain access to a code.

To get access to the image use your container orchestrator to pull from the following locations:

```
ghcr.io/nfrastack/container-nextcloud:(image_tag)
docker.io/nfrastack/nextcloud:(image_tag)
```

Image tag syntax is:

`<image>:<nextcloud_version>-<optional tag>-<optional phpversion>`

Example:

`docker.io/nfrastack/container-nextcloud:35` or

`ghcr.io/nfrastack/container-nextcloud:35-1.0-php84`

* An optional `tag` may exist that matches the [CHANGELOG](CHANGELOG.md) - These are the safest
Have a look at the container registries and see what tags are available.

#### Multi-Architecture Support

Images are built for `amd64` by default, with optional support for `arm64` and other architectures.

### Quick Start

* The quickest way to get started is using [docker-compose](https://docs.docker.com/compose/). See the examples folder for a working [compose.yml](examples/compose.yml) that can be modified for your use.

* Map [persistent storage](#persistent-storage) for access to configuration and data files for backup.
* Set various [environment variables](#environment-variables) to understand the capabilities of this image.

**The first boot can take from 2 minutes - 5 minutes depending on your CPU to setup the proper schemas.**

### Persistent Storage

The following directories/files should be mapped for persistent storage in order to utilize the container effectively.

| Directory               | Description                              |
| ----------------------- | ---------------------------------------- |
| `/data/userdata`        | Nextcloud Data                           |
| `/www/nextcloud/config` | Nextcloud Configuration Directory        |
| `/data/apps`            | Custom Apps downloaded from Plugin Store |
| `/data/themes`          | Custom Themes Directory                  |
| `/data/cache`           | Cache Directory                          |
| `/data/tmp`             | Temporary Directory                      |
| `/data/templates/`      | Templates Directory                      |
| `/logs`                 | Nextcloud logfiles                       |

### Environment Variables

#### Base Images used

This image relies on a customized base image in order to work.
Be sure to view the following repositories to understand all the customizable options:

| Image                                                                 | Description         |
| --------------------------------------------------------------------- | ------------------- |
| [OS Base](https://github.com/nfrastack/container-base/)               | Base Image          |
| [Nginx](https://github.com/nfrastack/container-nginx/)                | Nginx Webserver     |
| [Nginx PHP-FPM](https://github.com/nfrastack/container-nginx-php-fpm) | PHP-FPM Interpreter |

Below is the complete list of available options that can be used to customize your installation.

* Variables showing an 'x' under the `Advanced` column can only be set if the containers advanced functionality is enabled.

| Parameter                      | Description                                                                                                         | Default            | `_FILE` |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------ | ------- |
| `ADMIN_USER`                   | Admin user e.g. `admin`                                                                                             |                    | x       |
| `ADMIN_PASS`                   | Admin pass e.g. `password`                                                                                          |                    | x       |
| `DOMAIN`                       | The Domain that this is configured for e.g. 'example.org'                                                           |                    |         |
| `DB_TYPE`                      | Set the DB_TYPE - e.g. `mysql`, `postgres`, `sqlite3`                                                               | `sqlite3`          |         |
| `DB_HOST`                      | Hostname of DB Server e.g. `nextcloud-db`                                                                           |                    | x       |
| `DB_NAME`                      | Database name e.g. `nextcloud`                                                                                      |                    | x       |
| `DB_PORT`                      | Database port e.g. `3306`                                                                                           |                    | x       |
| `DB_USER`                      | Username for Database e.g. `nextcloud`                                                                              |                    | x       |
| `DB_PASS`                      | Password for Database e.g. `password`                                                                               |                    | x       |
| `IFRAME_DOMAINS`               | Comma seperated value of domains/hostnames you want to allow loading the site via an IFrame e.g. `site.example.com` |                    |         |
| `MONITORING_APPLICATION_TOKEN` | Monitoring Application Token if 'serverinfo' application installed                                                  |                    | x       |
| `NEXTCLOUD_USER`               | User for accessing and writing Nextcloud Files                                                                      | `${NGINX_USER}`    |         |
| `NEXTCLOUD_GROUP`              | Group for accessing Nextcloud Files                                                                                 | `${NGINX_GROUP}`   |         |
| `NEXTCLOUD_WEBROOT`            | Webroot for Nextcloud files                                                                                         | `${NGINX_WEBROOT}` |         |
| `REDIS_HOST`                   | Redis Hostname for Caching (`ENABLE_CACHE=TRUE`)                                                                    | ``                 |
| `REDIS_PORT`                   | Redis Port for Caching                                                                                              | `6379`             |
| `REDIS_DB`                     | Redis Database Number                                                                                               | `0`                |
| `REDIS_PASS`                   | (optional) Redis Passphrase                                                                                         | ``                 |


This image automatically configures nextcloud with the following options as defined in [config.sample.php](https://github.com/nextcloud/server/blob/master/config/config.sample.php).

| Variable        | Description              | Default                           |
| --------------- | ------------------------ | --------------------------------- |
| `DATA_PATH`     | Data Directory           | `${DATA_PATH}/userdata`           |
| `APP_PATH`      | Custom Applications      | `${DATA_PATH}/apps`               |
| `CACHE_PATH`    | Temporary Cache          | `${DATA_PATH}/cache`              |
| `USERDATA_PATH` | User and Group Data      | `${DATA_PATH}/userdata`           |
| `SKELETON_PATH` | Initial Profile Skeleton | `${DATA_PATH}/templates/skeleton` |
| `TEMP_PATH`     | Temporary Files          | `${DATA_PATH}/tmp`                |

| Variable                           | Description                                         | Nextcloud Attribute                         | Default            |
| ---------------------------------- | --------------------------------------------------- | ------------------------------------------- | ------------------ |
| `ANIMATED_GIFS_MAX_FILESIZE`       | Max file size for Animated Gifs                     | `max_filesize_animated_gifs_public_sharing` | `10`               |
| `CIPHER`                           | Encryption Cipher                                   | `cipher`                                    | `AES-256-CFB`      |
| `DEFAULT_APPLICATION`              | Default Application users see                       | `defaultapp`                                | `files`            |
| `DEFAULT_LANGUAGE`                 | Default Language to Install                         | `default_language`                          | `en`               |
| `DEFAULT_LOCALE`                   | Default Locale                                      | `default_locale`                            | `en_US`            |
| `ENABLE_APP_CODE_CHECKER`          | Check for signatures on all code                    | `appcodechecker`                            | `TRUE`             |
| `ENABLE_APP_STORE`                 | Enable App Store                                    | `appstoreenabled`                           | `TRUE`             |
| `ENABLE_BRUTEFORCE_PROTECTION`     | Enable Brute Force Protection                       | `auth.bruteforce.protection.enabled`        | `TRUE`             |
| `ENABLE_CACHE`                     | Enable APCu and or Redis Caching                    | `TRUE`                                      |                    |
| `ENABLE_CHECK_WELLKNOWN`           | Enable .wellknown check in admin                    |                                             | `TRUE`             |
| `ENABLE_DISPLAY_NAME_CHANGE`       | Allow user to change name                           | `allow_user_to_change_display_name`         | `TRUE`             |
| `ENABLE_FILESYSTEM_CHECK_CHANGES`  | Check changes on Filesystem in Background           | `filesystem_check_changes`                  | `FALSE`            |
| `ENABLE_FILE_LOCKING`              | Enable File Locking                                 | `filelocking.enabled`                       | `TRUE`             |
| `ENABLE_KNOWLEDGE_BASE`            | Enable Help Menu                                    | `knowledgebaseenabled`                      | `TRUE`             |
| `ENABLE_LOGIN_FORM_AUTOCOMPLETE`   | Allow Autocomplete on Login Pages                   | `login_form_autocomplete`                   | `TRUE`             |
| `ENABLE_LOG_QUERY`                 | Enable Logging DB Queries                           | `log_query`                                 | `FALSE`            |
| `ENABLE_MAINTENANCE_WINDOW`        | Enable Maintenance Window                           | `TRUE`                                      |                    |
| `ENABLE_MYSQL_UTF8MB4`             | Enable 4 byte strings for MariaDB                   | `mysql.utf8mb4`                             | `TRUE`             |
| `ENABLE_PARTFILE_UPLOADS_STORAGE`  | Enable uploading .part files in same location       |                                             | `TRUE`             |
| `ENABLE_PREVIEWS`                  | Enable Document Previews                            | `enable_previews`                           | `TRUE`             |
| `ENABLE_SESSION_KEEPALIVE`         | Enable Keepalive sessions                           | `session_keepalive`                         | `TRUE`             |
| `ENABLE_SIGN_UP`                   | Allow Signups to Instance                           | `simpleSignUpLink.shown`                    | `TRUE`             |
| `ENABLE_SMTP_AUTHENTICATION`       | Enable SMTP Authentication                          | `mail_smtpauth`                             | `FALSE`            |
| `ENABLE_TOKEN_AUTH_ENFORCED`       | Enforce logging in with Tokens for Clients          | `token_auth_enforced`                       | `FALSE`            |
| `FILE_LOCKING_DEBUG`               | Debug File Locking                                  | `filelocking.debug`                         | `FALSE`            |
| `FORCE_LANGUAGE`                   | Force Language                                      | `force_language`                            | `en`               |
| `FORCE_LOCALE`                     | Force Locale                                        | `force_locale`                              | `en_US`            |
| `HASHING_COST`                     | Performance Cost for Hashing                        | `hashingCost`                               | `10`               |
| `LDAP_CLEANUP_INTERVAL`            | How many minutes to cleanup LDAP users              | `ldapUserCleanupInterval`                   | `51`               |
| `LOG_DATE_FORMAT`                  | Format for Date and Time in logs                    | `logdateformat`                             | `Y-m-d H:i:s`      |
| `LOG_DIR`                          | Log Directory                                       | `logfile`                                   | `/logs/nextcloud`  |
| `LOG_FILE_AUDIT`                   | Audit Log file                                      | `audit.log`                                 |                    |
| `LOG_FILE_FLOW`                    | Workflow Log File                                   | `flow.log`                                  |                    |
| `LOG_LEVEL`                        | Log Level                                           | `loglevel`                                  | `2`                |
| `LOG_TIMEZONE`                     | Timezone for Logfile                                | `logtimezone`                               | Container Timezone |
| `MAINTENANCE_WINDOW_START_HOUR`    | What 4 hour time window to start maintenance window | `01`                                        |
| `OVERWRITE_HOST`                   | Override the hostname for URLs                      | `overwritehost`                             | ``                 |
| `OVERWRITE_PROTOCOL`               | Override the Protocol if behind proxy               | `overwriteprotocol`                         | ``                 |
| `PREVIEW_MAX_X`                    | Maximum Pixels for Previews (X)                     | `preview_max_x`                             | `200`              |
| `PREVIEW_MAX_Y`                    | Maximum Pixels for Previews (Y)                     | `preview_max_y`                             | `200`              |
| `SERVER_ID`                        | Server ID                                |                                             |
| `SHARE_FOLDER`                     | Change root path of all shares to users             |                                             | `/`                |
| `SHARING_ENABLE_ACCEPT`            | Enable Sharing Acceptance features                  |                                             | `FALSE`            |
| `SHARING_ENABLE_MAIL`              | Enable Sharing Mail Notification                    |                                             | `TRUE`             |
| `SHARING_FORCE_ACCEPT`             | Force Sharing Acceptance features                   |                                             | `FALSE`            |
| `SHARING_MAX_AUTOCOMPLETE_RESULTS` | Maximum results returned when searching             | `sharing.maxAutocompleteResults`            | `0`                |
| `SHARING_MIN_SEARCHSTRING_LENGTH`  | How many characters to type before searching        | `sharing.minSearchStringLength`             | `2`                |
| `SMTP_AUTHENTICATION_TYPE`         | Type of SMTP Authentication                         | `mail_smtpauthtype`                         | `NONE`             |
| `SMTP_DEBUG`                       | Debug SMTP activities                               | `mail_smtpdebug`                            | `FALSE`            |
| `SMTP_DOMAIN`                      | Email Domain for Outbound mail                      | `mail_domain`                               | `example.org`      |
| `SMTP_FROM`                        | Account name for Outbound Email                     | `mail_from_address`                         | `noreply`          |
| `SMTP_HOST`                        | Remote SMTP Host                                    | `mail_smtphost`                             | `postfix_relay`    |
| `SMTP_PASS`                        | SMTP Password                                       | `mail_smtppassword`                         | ``                 |
| `SMTP_PORT`                        | SMTP Port                                           | `mail_smtpport`                             | `25`               |
| `SMTP_SECURE`                      | Set if SMTP is TLS/SSL encrypted                    | `mail_smtpsecure`                           | `FALSE`            |
| `SMTP_SEND_PLAINTEXT_ONLY`         | Send Plaintext Email Only                           | `mail_send_plaintext_only`                  | `FALSE`            |
| `SMTP_TIMEOUT`                     | Timeout for SMTP connections                        | `mail_smtptimeout`                          | `10`               |
| `SMTP_USER`                        | SMTP Username                                       | `mail_smtpname`                             | ``                 |
| `SORT_GROUPS_BY_NAME`              | Sort groups by name as opposed to most users        | `sort_groups_by_name`                       | `FALSE`            |
| `TRASHBIN_RETENTION`               | How to deal with users trashbins                    | `trashbin_retention_obligation`             | `auto`             |
| `VERSIONS_RETENTION`               | How to deal with File Versions                      | `versions_retention_obligation`             | `auto`             |


* * *
## Maintenance

### Shell Access

For debugging and maintenance purposes you may want access the containers shell.

``bash
docker exec -it (whatever your container name is) bash
``

Once inside, you can execute the `occ` command from anywhere and it will operate as the web server user and from the nextcloud webroot path.

## Support & Maintenance

- For community help, tips, and community discussions, visit the [Discussions board](/discussions).
- For personalized support or a support agreement, see [Nfrastack Support](https://nfrastack.com/).
- To report bugs, submit a [Bug Report](issues/new). Usage questions will be closed as not-a-bug.
- Feature requests are welcome, but not guaranteed. For prioritized development, consider a support agreement.
- Updates are best-effort, with priority given to active production use and support agreements.

## References

* https://nextcloud.com

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

