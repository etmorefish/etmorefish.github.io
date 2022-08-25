---
title: Single Version installation
tags:
  - k8s
  - influxdb
  - cadvisor
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-18 18:33:18
---

# Single Version

- install victoriametrics

  ```sh
  docker run -it -d \
  	--name vm \
      -v /root/vm:/victoria-metrics-data \
      -p 8428:8428 \
      victoriametrics/victoria-metrics:latest
  
  ```

  

- install influxdb(latest)

  ```sh
  docker run -p 8086:8086 -d  \
  	--name influxdb
  	-v /var/lib/influxdb2:/var/lib/influxdb2  \
      -e DOCKER_INFLUXDB_INIT_USERNAME=root    \
      -e DOCKER_INFLUXDB_INIT_PASSWORD=huayun@123   \
      -e DOCKER_INFLUXDB_INIT_ORG=huayun   \
      -e DOCKER_INFLUXDB_INIT_BUCKET=prometheus   \
      influxdb:latest
  ```

  

- install cadvisor

  ```sh
  docker run -d \
      --name cadvisor \
      -p 8080:8080 \
      -v /:/rootfs:ro \
      -v /var/run/:/var/run/:rw \
      -v /sys/:/sys/:ro \
      -v /var/lib/docker/:/var/lib/docker/:ro \
      -v /dev/disk/:/dev/disk/:ro \
      google/cadvisor:latest
  ```

  