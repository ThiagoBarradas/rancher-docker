## Getting Started

1. Running Cluster
    1. Register [hosts](hosts) in your local machine;
    1. Run `docker-compose up -d`;
    1. If you want run all tests offline, run `docker exec -it docker-host-01 sh /start-offline-host.sh` and `docker exec -it docker-host-02 sh /start-offline-host.sh`;
    1. Wait a few moments and open in your browser `http://rancher-server`;
1. Setup Access Control and Hosts  
    1. Setup Access Control as Local and choose a username and password. `Admin > Access Control > Local`;
    1. Go to `Admin > Settings` and change *Host Registration URL* to `http://172.18.0.3:8080` (IP and Port in defined docker-compose.yml);
    1. Go to `Infrastructure > Hosts > Add Hosts` and choose `Custom`. Copy code in step 5 (without sudo);
    1. Connect in `docker-host-01` using `docker exec -it docker-host-01 sh` and run copied command;
    1. Do the previous two step for add `docker-host-02`;
    1. Wait for all containers to ready;
1. Creating API Services
    1. Go to `Stack > All` and `Add Stack` with name `api`;
    1. Create `apione` service using `thiagobarradas/demo-api-get-env:latest` image; Add environment variables `apione=it's me` `apitwo=it's not me`;
    1. Create `apitwo` service using `thiagobarradas/demo-api-get-env:latest` image; Add environment variables `apione=it's not me` `apitwo=it's me`;
1. Creating Load Balancer
    1. Go to `Stack > All` and `Add Stack` with name `load-balancer`;
    1. Create `loadbalancer` service using dropdown menu in `Add Service`, choose `Add Load Balancer`; 
    1. Choose `always run one instance of this container on every host` scale option;
    1. Add five `Service Rules`:
        1. `HTTP apione.com:80` to `api/apione:80` with empty path;
        1. `HTTP apitwo.com:80` to `api/apitwo:80` with empty path;
        1. `HTTP *:80` to `api/apione:80` with path: `/environment/apione`;
        1. `HTTP *:80` to `api/apitwo:80` with path: `/environment/apitwo`;
        1. `HTTP *:80` to `api/apione:80` with empty path;
1. Some Tests
    1. Access `Stack > All`; Choose a service (apione or apitwo), click to view details; and scale up clicking at `+` in `Scale`;
    1. You can all containers and yours stats (cpu, mem, etc);
    1. In each container, you can click in `...` to `View Logs` or `Execute Shell`; If you hold `ctrl` the new tab will be opened in a new window; Better to see next step. Open all containers logs and keep all tabs visible;
    1. In your browser (or using postman) send some requests to:
        1. `http://apione.com/environment/apione`
        1. `http://apione.com/environment/apitwo`
        1. `http://apitwo.com/environment/apione`
        1. `http://apitwo.com/environment/apitwo`
        1. `http://localhost/environment/apione`
        1. `http://localhost/environment/apitwo`