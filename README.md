# Container monitoring with cAdvisor, prometheus, and grafana 

 ![Docker](https://img.shields.io/badge/Docker-v24.0-%232496ED.svg?style=for-the-badge&logo=docker&logoColor=white) ![cAdvisor](https://img.shields.io/badge/cAdvisor-v0.47.2-%234CAEEA.svg?style=for-the-badge&logo=google&logoColor=white) ![Prometheus](https://img.shields.io/badge/Prometheus-v2.48-%23E6522C.svg?style=for-the-badge&logo=prometheus&logoColor=white) ![Grafana](https://img.shields.io/badge/Grafana-v10.2-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white) 



![Container monitoring](https://github.com/1DevOps2/grafana-Prometheus/assets/105498424/c5c4a81e-559b-420b-960e-d368db713903)

## Introduction

In today's digital landscape, efficient container service monitoring is crucial for ensuring the reliability and performance of your applications. To achieve this, we'll explore the setup of three essential tools: cAdvisor, Prometheus, and Grafana. This article aims to provide an easy-to-understand guide on setting up these monitoring tools to keep a watchful eye on your containerized services.

### 1. cAdvisor
cAdvisor, short for Container Advisor, is an open-source monitoring and performance analysis tool designed specifically for container environments. It allows you to gain insights into the resource usage and performance of containers running on your host system. Think of it as your container's personal health tracker, measuring CPU, memory, disk I/O, and network statistics. Unlike Node Exporter, which is tailored for monitoring the host itself, cAdvisor focuses on monitoring containerized applications.

#### Difference between cAdvisor and Node Exporter
The key difference between cAdvisor and Node Exporter lies in their scope. cAdvisor is container-centric, focusing on the health and performance of individual containers. In contrast, Node Exporter concentrates on the host system's metrics, including CPU, memory, and network usage, without container-specific insights. Depending on your monitoring needs, you may use both tools in tandem to achieve comprehensive insights into your entire environment.

### 2. Prometheus
Prometheus is a high-performance, open-source monitoring and alerting toolkit. It is the brain behind collecting and storing metrics from various sources, making it an ideal companion for cAdvisor. Prometheus scrapes and stores time-series data, enabling you to query and visualize these metrics through Grafana. With Prometheus, you can set up alerts and take timely actions to maintain the health of your containerized applications.

### 3. Grafana:
Grafana is a popular open-source platform for monitoring and observability, offering powerful visualization and dashboarding capabilities. It acts as the eyes of your monitoring setup, providing user-friendly, customizable dashboards to display the metrics collected by Prometheus. Grafana allows you to create visually appealing charts and graphs that help you gain insights into your container services' performance and resource usage.

## Setup
### Configure docker daemon
To monitor docker engine, we need to configure the Docker daemon on that server where ```containerized apps/services``` are running. you need to specify the metrics-address. The best way to do this is via the daemon.json, which is located at one of the following locations by default. If the file doesn't exist, create it. 

```
sudo nano /etc/docker.daemon.json
```
In order for Prometheus to gather the metrics of the docker we need to add below content in the ```/etc/docker/daemon.json``` file.
```
{
  "metrics-addr" : "0.0.0.0:9323",
  "experimental" : true
}
```
now restart the docker
```
sudo systemctl restart docker
```
### Running Wordpress app
To demonstrate this tutorial, i will install WordPress as a exmaple using Docker Compose. Two essential containers/services: the ```WordPress``` application and the ```MySQL``` database.
create a direcotry, called ```wordpress``` and create docker-compose file called ```docker-compose.yaml```
```
mkdir wordpress; cd wordpress; nano docker-compose.yaml
```
add the following content in to that file and save it.
```
version: '3.8'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 80:80
      - 443:443
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: wordpress_password
      WORDPRESS_DB_NAME: mydb
    volumes:
      - wordpress:/var/www/html

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: mydb
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: wordpress_password
      MYSQL_RANDOM_ROOT_PASSWORD: wordpress_root_password
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:
```
run the wordpress app
```
docker-compose up -d
```
### Installing cAdvisor
We will install cAdvisor on the same host where your ```containerized apps/services``` are running. cAdvisor acts as an agent, collecting real-time data on container performance, resource utilization, and other critical metrics. Although a common practice is to install cAdvisor directly on the host, we will demonstrate the Docker installation method for simplicity and consistency.
Install cadvisor using docker, run below command:
```
docker run -d \
  --name cadvisor \
  --privileged=true \
  -p 8080:8080 \
  -v /var/run:/var/run:rw \
  -v /var/lib/docker:/var/lib/docker:ro \
  gcr.io/cadvisor/cadvisor:v0.47.2
```
### Installing Prometheus and Grafana
Both Prometheus and Grafana will be installed on separate instances using Docker containers. This separation allows for better scalability and isolation. Prometheus will scrape metrics from cAdvisor to store and query the data, while Grafana will create visual dashboards based on these metrics.

Create directory ```prom_grafana``` and create ```docker-compose.yaml```.
```
mkdir prom_grafana; cd prom_grafana; sudo nano docker-compose.yaml
```
add the following content in yaml file
```
version: '3'

services:
  # define prometheus service
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - 9090:9090
    command:
      - --config.file=/etc/prometheus/prometheus.yml
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

  # define grafana service
  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - 3000:3000
    volumes:
      - grafana:/var/lib/grafana
```
### Defining Endpoints in Prometheus
In the prometheus.yml configuration file, you will define endpoints for cAdvisor. These endpoints specify where Prometheus can collect metrics from cAdvisor. Prometheus will periodically scrape these endpoints to keep its metrics up to date. This is the crucial link that connects cAdvisor's data to Prometheus.
open file:
```
sudo nano prometheus.yml
```
add the following content in ```prometheus.yml``` file:
```
global:
  scrape_interval: 30s 

scrape_configs:

  - job_name: "prometheus"
    honor_labels: true
    static_configs:
    - targets: 
        - prometheus:9090
  
  - job_name: "docker"
    honor_labels: true
    static_configs:
    - targets: 
        - localhost:9323

  - job_name: "cadvisor"
    honor_labels: true
    static_configs:
    - targets: 
        - localhost:8080
```
make sure to change the ```ip addresses```. Now run the container:
```
docker-compose up -d
```
now configure the grafana for prometheus.

### Visualizing Metrics with Grafana
After setting up Prometheus, Grafana will come into play. You will create Grafana dashboards and configure them to pull data from Prometheus. These dashboards will provide a user-friendly interface to visualize and explore the metrics collected from your containerized services. To configure the Grafana dashboard, I downloaded several useful JSON files from the official Grafana website, available in this repo.

## Access Points
For docker engine daemon --> ```localhost:9323/metrics```.

For cAdvisor --> ```localhost:8080/metrics```

For Prometheus --> ```localhost:9090```

For grafana --> ```localhost:3000``` (Default Credentials: ```admin:admin```)

## Ports 
if you are using firewall, make sure to add these ports:
```
9323 8080 9090 3000
```



