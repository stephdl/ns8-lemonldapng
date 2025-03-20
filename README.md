# ns8-lemonldapng

## Install

Instantiate the module with:

    add-module ghcr.io/nethserver/lemonldapng:latest 1

The output of the command will return the instance name.
Output example:

    {"module_id": "lemonldapng1", "image_name": "lemonldapng", "image_url": "ghcr.io/nethserver/lemonldapng:latest"}

## Documentation

The complete documentation of the LemonLdap:NG software is available in the [official website](https://lemonldap-ng.org/documentation/latest/)

## Applications

How to integrate [Applications](https://lemonldap-ng.org/documentation/latest/applications.html)

## auth and manager portal

By default it exists some virtual hosts to show you configuration examples, you need to create some DNS entries pointed to the IP of your server, replace `domain.com` by the `host` value you have set

- auth.domain.com
- manager.domain.com
- test1.domain.com
- test2.domain.com
- lemonldapng.domain.com

The `auth` portal displays examples of applications accessible to both privileged and regular users. The `manager` portal, however, requires access rights granted to the domain admins group.

## LDAP auto discovery

The LDAP settings are automatically discovered upon service restart, enabling login with the `administrator` user and any member of the `domain admins` group as an administrator. All other LDAP users can access the portal.

## Customisation

The `./llng` folder within the state directory stores customizations such as themes and logos. This folder is included in the backup archive.

```
./llng/
├── auth
├── captcha
├── menutab
├── plugins
├── register
├── template
├── theme
└── userdb
```

## Configure

Let's assume that the mattermost instance is named `lemonldapng1`.

Launch `configure-module`, by setting the following parameters:
- `host`: a fully qualified domain name for the application
- `http2https`: enable or disable HTTP to HTTPS redirection (true/false)
- `lets_encrypt`: enable or disable Let's Encrypt certificate (true/false)
- `ldap_domain`: set the LDAP domain


Example:

```
api-cli run configure-module --agent module/lemonldapng1 --data - <<EOF
{
  "host": "lemonldapng.domain.com",
  "http2https": true,
  "lets_encrypt": false,
  "ldap_domain": "domain.com"
}
EOF
```

The above command will:
- start and configure the lemonldapng instance
- configure a virtual host for trafik to access the instance

## Get the configuration
You can retrieve the configuration with

```
api-cli run get-configuration --agent module/lemonldapng1
```

## Uninstall

To uninstall the instance:

    remove-module --no-preserve lemonldapng1

## Smarthost setting discovery

Some configuration settings, like the smarthost setup, are not part of the
`configure-module` action input: they are discovered by looking at some
Redis keys.  To ensure the module is always up-to-date with the
centralized [smarthost
setup](https://nethserver.github.io/ns8-core/core/smarthost/) every time
lemonldapng starts, the command `bin/discover-smarthost` runs and refreshes
the `state/smarthost.env` file with fresh values from Redis.

Furthermore if smarthost setup is changed when lemonldapng is already
running, the event handler `events/smarthost-changed/10reload_services`
restarts the main module service.

See also the `systemd/user/lemonldapng.service` file.

This setting discovery is just an example to understand how the module is
expected to work: it can be rewritten or discarded completely.

## Debug

some CLI are needed to debug

- The module runs under an agent that initiate a lot of environment variables (in /home/lemonldapng1/.config/state), it could be nice to verify them
on the root terminal

    `runagent -m lemonldapng1 env`

- you can become runagent for testing scripts and initiate all environment variables
  
    `runagent -m lemonldapng1`

 the path become : 
```
    echo $PATH
    /home/lemonldapng1/.config/bin:/usr/local/agent/pyenv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/
```

- if you want to debug a container or see environment inside
 `runagent -m lemonldapng1`
 ```
podman ps
CONTAINER ID  IMAGE                                      COMMAND               CREATED        STATUS        PORTS                    NAMES
d292c6ff28e9  localhost/podman-pause:4.6.1-1702418000                          9 minutes ago  Up 9 minutes  127.0.0.1:20015->80/tcp  80b8de25945f-infra
d8df02bf6f4a  docker.io/library/mariadb:10.11.5          --character-set-s...  9 minutes ago  Up 9 minutes  127.0.0.1:20015->80/tcp  mariadb-app
9e58e5bd676f  docker.io/library/nginx:stable-alpine3.17  nginx -g daemon o...  9 minutes ago  Up 9 minutes  127.0.0.1:20015->80/tcp  lemonldapng-app
```

you can see what environment variable is inside the container
```
podman exec  lemonldapng-app env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
TERM=xterm
PKG_RELEASE=1
MARIADB_DB_HOST=127.0.0.1
MARIADB_DB_NAME=lemonldapng
MARIADB_IMAGE=docker.io/mariadb:10.11.5
MARIADB_DB_TYPE=mysql
container=podman
NGINX_VERSION=1.24.0
NJS_VERSION=0.7.12
MARIADB_DB_USER=lemonldapng
MARIADB_DB_PASSWORD=lemonldapng
MARIADB_DB_PORT=3306
HOME=/root
```

you can run a shell inside the container

```
podman exec -ti   lemonldapng-app sh
/ # 
```
## Testing

Test the module using the `test-module.sh` script:


    ./test-module.sh <NODE_ADDR> ghcr.io/nethserver/lemonldapng:latest

The tests are made using [Robot Framework](https://robotframework.org/)

## UI translation

Translated with [Weblate](https://hosted.weblate.org/projects/ns8/).

To setup the translation process:

- add [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- add your repository to [hosted.weblate.org]((https://hosted.weblate.org) or ask a NethServer developer to add it to ns8 Weblate project
