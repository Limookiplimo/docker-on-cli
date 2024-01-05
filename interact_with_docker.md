This is a brief interaction with a docker container on terminal.

- Start the postgresql service
    ```
    $ docker compose up -d

    [+] Running 3/3
    ✔ Network docker-on-cli_default              Created                                                                                                                                                         0.1s 
    ✔ Volume "docker-on-cli_postgres-db-volume"  Created                                                                                                                                                         0.0s 
    ✔ Container postgres_db                      Started                                                                                                                                                         0.0s 
    ```

- Is the postgresql database service up and running?
    ```
    $ docker ps  

    CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
    fbc5ac11218a   postgres:latest   "docker-entrypoint.s…"   36 seconds ago   Up 36 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres_db
    ```

- What is the IP address for the Postgresql service?
    ```
    $ docker inspect postgres_db| grep IPAddress
                "SecondaryIPAddresses": null,
                "IPAddress": "",
                        "IPAddress": "172.22.0.2",
    ```
- Can you execute SQL query commands inside the postgres_db container?
    ```
    $ docker exec -it postgres_db psql -U user -d demo_db                                                          
    psql (16.1 (Debian 16.1-1.pgdg120+1))
    Type "help" for help.

    demo_db=#
    ```
     - create a users table:
        ```
        demo_db=# CREATE TABLE users (
            user_id BIGINT PRIMARY KEY,
            username VARCHAR(50) NOT NULL,
            email VARCHAR(100) NOT NULL,
            password VARCHAR(255) NOT NULL,
            created_at TIMESTAMP DEFAULT current_timestamp
        );
        CREATE TABLE
        ```
    - check created table:
        ```
        demo_db=# select * from users;
        user_id | username | email | password | created_at 
        ---------+----------+-------+----------+------------
        (0 rows)
        ```
- Can you check the postgres_db container directories?
    ```
    $ docker exec -it postgres_db /bin/bash 
    root@fbc5ac11218a:/# ls
    bin  boot  dev	docker-entrypoint-initdb.d  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
    ```
- What is the Write-Ahead Logging (WAL) level set to on the postgres_db container?
    ```
    demo_db=# show wal_level;
    wal_level 
    -----------
    replica
    (1 row)
    ```
- Can you update the WAL level from replica to logical?
    ```
    $ docker exec -it postgres_db /bin/bash 
    root@fbc5ac11218a:/# vi /var/lib/postgresql/data/postgresql.conf
    ```

    - the postgresql.conf update:
        ```
        #------------------------------------------------------------------------------
        # WRITE-AHEAD LOG
        #------------------------------------------------------------------------------

        # - Settings -

        wal_level = logical                              #minimal, replica or logical
        ```
        - save the changes:
            `:wq`
        
        For changes to take effect, restart the container and check the wal_level.

- When done trying docker on terminal, can you cleanup?
    ```
    $ docker compose down --rmi all --volumes                                
    [+] Running 4/4
    ✔ Container postgres_db                    Removed                                                                                                                                                           0.3s 
    ✔ Volume docker-on-cli_postgres-db-volume  Removed                                                                                                                                                           0.0s 
    ✔ Image postgres:latest                    Removed                                                                                                                                                           0.2s 
    ✔ Network docker-on-cli_default            Removed                                                                                                                                                           0.4s 
    ```

### Research more, and try more commands!




























