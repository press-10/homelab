
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


Make sure that homeserver.yaml is accessible by matrix (drop it into /matrix_synapse)