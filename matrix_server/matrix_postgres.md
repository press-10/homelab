
## Matrix w/ Postgres DB

Here's the stack:

```version: "3.3"

services:
    synapse:
        image: "matrixdotorg/synapse:latest"
        container_name: "synapse"
        restart: unless-stopped
        ports:
            - 8008:8008
        volumes:
            - /matrix_synapse:/data
        
    db:
      image: docker.io/postgres:latest
      restart: unless-stopped
      environment:
        - POSTGRES_USER=supersecureusernamehere
        - POSTGRES_PASSWORD=supersecurepasswordhere
        - POSTGRES_INITDB_ARGS=--encoding='UTF8' --lc-collate='C' --lc-ctype='C'
      volumes:
        - /postgres_synapse/:/var/lib/postgresql/data


```


## homeserver.yaml
Make sure you have this accessible by synapse.
```# Configuration file for Synapse.
#
# This is a YAML file: see [1] for a quick introduction. Note in particular
# that *indentation is important*: all the elements of a list or dictionary
# should have the same indentation.
#
# [1] https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html
#
# For more information on how to configure Synapse, including a complete accounting of
# each option, go to docs/usage/configuration/config_documentation.md or
# https://element-hq.github.io/synapse/latest/usage/configuration/config_documentation.html
server_name: "subdomain.domain.tld"
pid_file: /data/homeserver.pid
listeners:
  - port: 8008
    tls: false
    type: http
    x_forwarded: true
    resources:
      - names: [client, federation]
        compress: false
#database:
#  name: sqlite3
#  args:
#    database: /data/homeserver.db
database:
  name: psycopg2
  args:
    user: samesupersecureusernameasthestack # Same username defined above
    password: samesupersecurepasswordasthestack # Same password defined above
    database: synapse
    host: db # The service name defined in your docker-compose file
    cp_min: 5
    cp_max: 10


log_config: "/data/matrix.ihavenoname.xyz.log.config"
media_store_path: /data/media_store
registration_shared_secret: "hunter2butallIseeis*******"
report_stats: true
macaroon_secret_key: "hunter2"
form_secret: "another_super_secure_password"
signing_key_path: "/data/subdomain.domain.tld.signing.key"
trusted_key_servers:
  - server_name: "matrix.org"
enable_registration: true
registration_requires_token: true
app_service_config_files:
  - "/data/discord-registration.yaml"
use_appservice_legacy_authorization: true

# vim:ft=yaml

```

