# Container monitoring with cAdvisor, prometheus, and grafana 

 ![Docker](https://img.shields.io/badge/Docker-23.0.3-%232496ED.svg?style=for-the-badge&logo=docker&logoColor=white) ![cAdvisor](https://img.shields.io/badge/cAdvisor-0.47-%234CAEEA.svg?style=for-the-badge&logo=google&logoColor=white) ![Prometheus](https://img.shields.io/badge/Prometheus-latest-%23E6522C.svg?style=for-the-badge&logo=prometheus&logoColor=white) ![Grafana](https://img.shields.io/badge/Grafana-latest-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white) 



![Container monitoring](https://github.com/1DevOps2/grafana-Prometheus/assets/105498424/c5c4a81e-559b-420b-960e-d368db713903)

## Introduction

In today's digital landscape, efficient container service monitoring is crucial for ensuring the reliability and performance of your applications. To achieve this, we'll explore the setup of three essential tools: cAdvisor, Prometheus, and Grafana. This article aims to provide an easy-to-understand guide on setting up these monitoring tools to keep a watchful eye on your containerized services.

### 1. cAdvisor:
cAdvisor, short for Container Advisor, is an open-source monitoring and performance analysis tool designed specifically for container environments. It allows you to gain insights into the resource usage and performance of containers running on your host system. Think of it as your container's personal health tracker, measuring CPU, memory, disk I/O, and network statistics. Unlike Node Exporter, which is tailored for monitoring the host itself, cAdvisor focuses on monitoring containerized applications.

#### Difference between cAdvisor and Node Exporter:
The key difference between cAdvisor and Node Exporter lies in their scope. cAdvisor is container-centric, focusing on the health and performance of individual containers. In contrast, Node Exporter concentrates on the host system's metrics, including CPU, memory, and network usage, without container-specific insights. Depending on your monitoring needs, you may use both tools in tandem to achieve comprehensive insights into your entire environment.

### 2. Prometheus:
Prometheus is a high-performance, open-source monitoring and alerting toolkit. It is the brain behind collecting and storing metrics from various sources, making it an ideal companion for cAdvisor. Prometheus scrapes and stores time-series data, enabling you to query and visualize these metrics through Grafana. With Prometheus, you can set up alerts and take timely actions to maintain the health of your containerized applications.

### 3. Grafana:
Grafana is a popular open-source platform for monitoring and observability, offering powerful visualization and dashboarding capabilities. It acts as the eyes of your monitoring setup, providing user-friendly, customizable dashboards to display the metrics collected by Prometheus. Grafana allows you to create visually appealing charts and graphs that help you gain insights into your container services' performance and resource usage.

## How the Setup Will Work:

To get started, we will follow these steps to set up the monitoring infrastructure:

### Installing cAdvisor: 
We will install cAdvisor on the same host where your ```containerized apps/services``` are running. cAdvisor acts as an agent, collecting real-time data on container performance, resource utilization, and other critical metrics. Although a common practice is to install cAdvisor directly on the host, we will demonstrate the Docker installation method for simplicity and consistency.

### Installing Prometheus and Grafana: 
Both Prometheus and Grafana will be installed on separate instances using Docker containers. This separation allows for better scalability and isolation. Prometheus will scrape metrics from cAdvisor to store and query the data, while Grafana will create visual dashboards based on these metrics.

### Defining Endpoints in Prometheus: 
In the prometheus.yml configuration file, you will define endpoints for cAdvisor. These endpoints specify where Prometheus can collect metrics from cAdvisor. Prometheus will periodically scrape these endpoints to keep its metrics up to date. This is the crucial link that connects cAdvisor's data to Prometheus.

### Visualizing Metrics with Grafana: 
After setting up Prometheus, Grafana will come into play. You will create Grafana dashboards and configure them to pull data from Prometheus. These dashboards will provide a user-friendly interface to visualize and explore the metrics collected from your containerized services.

## Setup

By following this setup, you will have a robust monitoring system in place, allowing you to keep a close watch on the health and performance of your containerized applications with ease. The combination of cAdvisor, Prometheus, and Grafana will empower you to make informed decisions and ensure the smooth operation of your container services.
