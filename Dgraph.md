# DGraph
**Fast, Transactional, Distributed Graph Database.**

## Installation
#### Step 1: Install Dgraph



If you're using Docker, you can use the  [official Dgraph image](https://hub.docker.com/r/dgraph/dgraph/).

    docker pull dgraph/dgraph:latest
#### Step 2: Run Dgraph
Docker Compose

     
     version: "3.2"
    services:
      zero:
        image: dgraph/dgraph:latest
        volumes:
          - type: volume
            source: dgraph
            target: /dgraph
            volume:
              nocopy: true
        ports:
          - 5080:5080
          - 6080:6080
        restart: on-failure
        command: dgraph zero --my=zero:5080
      server:
        image: dgraph/dgraph:latest
        volumes:
          - type: volume
            source: dgraph
            target: /dgraph
            volume:
              nocopy: true
        ports:
          - 8080:8080
          - 9080:9080
        restart: on-failure
        command: dgraph alpha --my=server:7080 --lru_mb=2048 --zero=zero:5080
      ratel:
        image: dgraph/dgraph:latest
        volumes:
          - type: volume
            source: dgraph
            target: /dgraph
            volume:
              nocopy: true
        ports:
          - 8000:8000
        command: dgraph-ratel
    
    volumes:
      dgraph:
Save the contents of the snippet above in a file called `docker-compose.yml`, then run the following command from the folder containing the file.

    docker-compose up -d
#### Step 3: Run Queries
Once Dgraph is running, you can access Ratel at [`http://localhost:8000`](http://localhost:8000/). It allows browser-based queries, mutations and visualizations.
