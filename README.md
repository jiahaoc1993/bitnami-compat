# Bitnami-Compat

[![Inactively Maintained](https://img.shields.io/badge/Maintenance%20Level-Inactively%20Maintained-yellowgreen.svg)](https://gist.github.com/cheerfulstoic/d107229326a01ff0f333a1d3476e068d)
[![Project-Stage](https://img.shields.io/badge/Project%20Stage-Development-yellowgreen.svg)](https://img.shields.io/badge/Project%20Stage-Development-yellowgreen.svg)
[![Production Ready](https://img.shields.io/badge/Production%20Ready-No-red.svg)](https://img.shields.io/badge/Production%20Ready-No-red.svg)
[![Update bitnami-dockers](https://github.com/ZCube/bitnami-compat/actions/workflows/update.yml/badge.svg)](https://github.com/ZCube/bitnami-compat/actions/workflows/update.yml)
[![Build](https://github.com/ZCube/bitnami-compat/actions/workflows/build-on-push.yml/badge.svg)](https://github.com/ZCube/bitnami-compat/actions/workflows/build-on-push.yml)
[![Chart Testing](https://github.com/ZCube/bitnami-compat/actions/workflows/test-charts.yaml/badge.svg)](https://github.com/ZCube/bitnami-compat/actions/workflows/test-charts.yaml)

## What is Bitnami-Compat?

* This repository builds multi-arch (`linux/amd64` + `linux/arm64`) docker images for some bitnami charts.

* This is a workaround for https://github.com/bitnami/charts/issues/7305.

* These images are NOT intended for production use.
  * This repository cannot be stable. Changes in multiple repositories (apt, bitnami, etc...) are reflected so images can break at any time.
  * This repository is mainly maintained for my homelab and development.

* These images use Bitnami's scripts. Binaries refer to the official Docker or official distribution binaries of the software or Docker recipes for Docker.

  * ![Bitnami-Compat](https://www.plantuml.com/plantuml/svg/XPH1pjem48NtSmgBJLUE4XTQLK88jOkgPwYw6E81B18xiXsXgkhTQySafFv_4bREctdFOhvCZiwZzNtR0BYRqXrQR9asaXnrR0SVdg95VOkYA07EGI2s-Quy8VlHueNOwJVxghp6LcqOEdQ99HKh5Vi3jMaxeqbxjWc5iQ5Y-xqeFdufIbv6l_DOtroh_FLmo2o4pzHeguhgSx5TiHdN9rSuF4oVrkvIMl6pQcWmXeTuJjvjkAzQibhIaYptr7OD-fIGj55AGULQq1sLvsTZUMTi6bPKWX2VnupTSeQhhUgy6zoFStG6nobH2kr_uKXQ1lOhGFcta8i-uofUSXKPL-py8b2H0PNchXk3abbg21sn2661hSZPcjxMuG_qgf7hT4L6zwLvdQz8RDQZ5WcBZ72UiiHOl6KCmwRDVjogozNp0njOGTgf5-K01uRrUTyVXm_JbkJrQSAGhy7KaWyGvc_w_s7p2S7qIkTtJeC0c7s2KW__ULLIaluL-vvL0UCU_0C0)

  * Some example : https://gist.github.com/ZCube/7e3045b1f15b1c1223f58267fc738e57

## Update bitnami-dockers schedule

* Every friday.

## Usage #1

* I know I can't make the images in this repository trustworthy, and I know I can't fix it. This repository is just a personal repository.
* If you want to use the images, It is recommended not to use the image directly, but to create Dockerfile.arm64 by following the instructions below and then verify and use it.

* Create Dockerfile.arm64 for bitnami-dockers
  1. install task https://taskfile.dev/installation/
  2. install golang https://go.dev/doc/install
  3. clone
  ```bash
  git clone --recursive https://github.com/ZCube/bitnami-compat
  cd bitnami-compat
  task generate
  or
  go run main.go generate --app=mongodb
  ```
  4. verify Dockerfile.arm64. Official images or build scripts referenced for build are listed in the table below.
  5. build & use. (docker buildx needed)
  ```
  task build
  or (current platform only)
  go run main.go build --app=mongodb --tag ghcr.io/zcube/bitnami-compat/
  or (current platform only && push)
  go run main.go build --app=mongodb --tag ghcr.io/zcube/bitnami-compat/ --push
  or (docker buildx needed && push)
  go run main.go build --app=mongodb --tag ghcr.io/zcube/bitnami-compat/ --push --platforms=linux/amd64,linux/arm64 

  docker run --rm -ti ghcr.io/zcube/bitnami-compat/mongodb:6.0
  ```

* PS. Rabbitmq and fluentd builds are too long. Depending on the machine, it is best to prepare for 1-2 hours.

## Usage #2

* TL;DR

from
```yaml
image:
  registry: docker.io
  repository: bitnami/postgresql
  tag: 11.16.0-debian-11-r14
```
to
```yaml
image:
  registry: ghcr.io
  repository: zcube/bitnami-compat/postgresql
  tag: 11.16.0-debian-11-r15
```
or
```bash
curl -LO https://raw.githubusercontent.com/ZCube/bitnami-compat/main/values/values_bitnami_compat_postgresql.yaml
helm install db bitnami/postgresql -f values_bitnami_compat_postgresql.yaml
```

## Test

* https://github.com/ZCube/bitnami-compat-test

* legend

  * O : test pass
  * X : test pass fail
  * Empty : not tested

* note
  * cypress v9 does not support arm64 so amd64 machine with ssh, docker needed to test.
  * arm64 test was done on mac mini m1

* result

    | App    | Tests   | bitnami (amd64) | bitnami-compat (amd64) | bitnami-compat (arm64) | official (amd64) | official (arm64) | official link |
    | :----- | :------ | :-------------: | :--------------------: | :--------------------: | :--------------: | :--------------: | :-----------: |
    | cert-manager  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | consul  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | consul  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | contour  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | discourse  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | discourse  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :x:     |                  |                  |               |
    | elasticsearch  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | elasticsearch  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | etcd  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | external-dns  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | fluentd  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | gitea  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | gitea  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | kafka  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | keycloak  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | keycloak  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | mariadb  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | mariadb-galera  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | memcached  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | minio  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | minio  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | mongodb  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | mongodb-sharded  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | mysql  | Goss    |  :heavy_check_mark: |  | :heavy_check_mark:     |                  |                  |               |
    | nginx  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | nginx  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | postgresql  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | postgresql-ha  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | rabbitmq  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | rabbitmq  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | rabbitmq-cluster-operator  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | rabbitmq-cluster-operator  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | redis  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | redis-cluster  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | sealed-secrets  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | solr  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | solr  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | tomcat  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | tomcat  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | wordpress  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | wordpress  | Cypress |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |
    | zookeeper  | Goss    |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:     |                  |                  |               |

## Supported bitnami charts

```
✔️  cert-manager
✔️  common
✔️  consul
✔️  contour
✔️  contour-operator
✔️  discourse
✔️  elasticsearch
✔️  etcd
✔️  external-dns
✔️  fluentd
✔️  gitea
✔️  kafka
✔️  keycloak
✔️  mariadb
✔️  mariadb-galera
✔️  memcached
✔️  minio
✔️  mongodb
✔️  mongodb-sharded
✔️  mysql
✔️  nginx
✔️  node
✔️  postgresql
✔️  postgresql-ha
✔️  rabbitmq
✔️  rabbitmq-cluster-operator
✔️  redis
✔️  redis-cluster
✔️  sealed-secrets
✔️  solr
✔️  thanos
✔️  tomcat
✔️  wordpress
✔️  zookeeper
❓  apache
  ❌  apache
  ✔️  git
  ✔️  apache-exporter
❓  nginx-ingress-controller
  ❌  nginx-ingress-controller
  ✔️  nginx
❓  nginx-intel
  ❌  nginx-intel
  ✔️  git
  ✔️  nginx-exporter
❓  wordpress-intel
  ❌  wordpress-intel
  ✔️  bitnami-shell
  ✔️  nginx-exporter
```

* For wordpress-intel charts, try using wordpress-nginx instead of wordpress-intel images. (wordpress-intel = wordpress-nginx)

## Binary References

|Package|Type|Reference|Base|
|--|--|--|--|
|acmesolver|DockerImage|https://quay.io/jetstack/cert-manager-acmesolver|bitnami/minideb|
|cainjector|DockerImage|https://quay.io/jetstack/cert-manager-cainjector|bitnami/minideb|
|cert-manager|DockerImage|https://quay.io/jetstack/cert-manager-controller|bitnami/minideb|
|cert-manager-webhook|DockerImage|https://quay.io/jetstack/cert-manager-webhook|bitnami/minideb|
|consul|DockerImage|https://hub.docker.com/_/consul|bitnami/minideb|
|consul-exporter|Dockerfile|https://hub.docker.com/r/prom/consul-exporter|bitnami/minideb|
|elasticsearch|Install|https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html|bitnami/minideb|
|etcd|Dockerfile|https://gcr.io/etcd-development/etcd|bitnami/minideb|
|fluentd|Dockerfile|https://github.com/fluent/fluentd-docker-image|bitnami/minideb|
|jmx-exporter|Install|https://repo1.maven.org/maven2/io/prometheus/jmx|bitnami/minideb|
|kafka|Install|https://downloads.apache.org/kafka|bitnami/minideb|
|kubectl|Install|https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/|bitnami/minideb|
|keycloak|Dockerfile|https://github.com/keycloak/keycloak-containers|bitnami/minideb|
|mariadb|Dockerfile|https://github.com/MariaDB/mariadb-docker|bitnami/minideb|
|mariadb-galera|Dockerfile|https://github.com/MariaDB/mariadb-docker|bitnami/minideb|
|memcached|Dockerfile|https://github.com/docker-library/memcached|bitnami/minideb|
|minio|DockerImage|https://hub.docker.com/r/minio/minio|bitnami/minideb|
|minio-client|DockerImage|https://hub.docker.com/r/minio/mc|bitnami/minideb|
|mongodb|Dockerfile|https://github.com/docker-library/mongo|bitnami/minideb|
|mysql|DockerImage|https://hub.docker.com/r/mysql|mysql/mysql-server|
|mysqld-exporter|Dockerfile|https://hub.docker.com/r/prom/mysqld-exporter|bitnami/minideb|
|nginx|Dockerfile|https://github.com/nginxinc/docker-nginx|bitnami/minideb|
|pgpool|Dockerfile|https://github.com/docker-library/postgres|bitnami/minideb|
|postgresql|Dockerfile|https://github.com/docker-library/postgres|bitnami/minideb|
|postgresql-repmgr|Dockerfile|https://github.com/docker-library/postgres|bitnami/minideb|
|rabbitmq|Dockerfile|https://github.com/docker-library/rabbitmq|bitnami/minideb|
|redis|Dockerfile|https://github.com/docker-library/redis|bitnami/minideb|
|redis-cluster|Dockerfile|https://github.com/docker-library/redis|bitnami/minideb|
|redis-exporter|DockerImage|https://hub.docker.com/r/oliver006/redis_exporter|bitnami/minideb|
|redis-sentinel|Dockerfile|https://github.com/docker-library/redis|bitnami/minideb|
|tomcat|Install|https://downloads.apache.org/tomcat|bitnami/minideb|
|wordpress|||bitnami/minideb|
|zookeeper|Install|https://downloads.apache.org/zookeeper|bitnami/minideb|

## Supported images and tags

* :warning: For RabbitMQ for ARM64, JIT is disabled for building on AMD64.

* :warning: For some images such as node and python, apache, java, the version can only match the major version. This is mostly the case for non-main apps, and I don't care because they're mostly dependencies for other apps.

* :warning: The mongodb provided by this repository does not currently work on Raspberry Pi 4. Use the image I distribute at https://github.com/ZCube/mongodb-for-armv8.0-a.

* acmesolver : [`1`, `1-debian-11`, `1.10.1`, `1.10.1-debian-11`, `1.10.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Facmesolver)
* apache-exporter : [`0`, `0-debian-11`, `0.11.0`, `0.11.0-debian-11`, `0.11.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fapache-exporter)
* bitnami-shell : [`11`, `11-debian-11`, `11.0.0`, `11.0.0-debian-11`, `11.0.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fbitnami-shell)
* cainjector : [`1`, `1-debian-11`, `1.10.1`, `1.10.1-debian-11`, `1.10.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcainjector)
* cert-manager : [`1`, `1-debian-11`, `1.10.1`, `1.10.1-debian-11`, `1.10.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcert-manager)
* cert-manager-webhook : [`1`, `1-debian-11`, `1.10.1`, `1.10.1-debian-11`, `1.10.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcert-manager-webhook)
* consul : [`1`, `1-debian-11`, `1.14.3`, `1.14.3-debian-11`, `1.14.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fconsul)
* consul-exporter : [`0`, `0-debian-11`, `0.9.0`, `0.9.0-debian-11`, `0.9.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fconsul-exporter)
* contour : [`1.21`, `1.21-debian-11`, `1.21.3`, `1.21.3-debian-11`, `1.21.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcontour)
* contour : [`1.22`, `1.22-debian-11`, `1.22.3`, `1.22.3-debian-11`, `1.22.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcontour)
* contour-operator : [`1.21`, `1.21-scratch`, `1.21.1`, `1.21.1-scratch`, `1.21.1-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcontour-operator)
* contour-operator : [`1.22`, `1.22-scratch`, `1.22.1`, `1.22.1-scratch`, `1.22.1-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fcontour-operator)
* discourse : [`2`, `2-debian-11`, `2.8.13`, `2.8.13-debian-11`, `2.8.13-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fdiscourse)
* elasticsearch : [`7`, `7-debian-11`, `7.17.8`, `7.17.8-debian-11`, `7.17.8-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Felasticsearch)
* elasticsearch-exporter : [`1`, `1-debian-11`, `1.5.0`, `1.5.0-debian-11`, `1.5.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Felasticsearch-exporter)
* envoy : [`1.21`, `1.21-debian-11`, `1.21.6`, `1.21.6-debian-11`, `1.21.6-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fenvoy)
* envoy : [`1.22`, `1.22-debian-11`, `1.22.6`, `1.22.6-debian-11`, `1.22.6-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fenvoy)
* envoy : [`1.23`, `1.23-debian-11`, `1.23.3`, `1.23.3-debian-11`, `1.23.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fenvoy)
* etcd : [`3.3`, `3.3-debian-11`, `3.3.27`, `3.3.27-debian-11`, `3.3.27-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fetcd)
* etcd : [`3.4`, `3.4-debian-11`, `3.4.23`, `3.4.23-debian-11`, `3.4.23-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fetcd)
* etcd : [`3.5`, `3.5-debian-11`, `3.5.6`, `3.5.6-debian-11`, `3.5.6-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fetcd)
* external-dns : [`0`, `0-debian-11`, `0.13.1`, `0.13.1-debian-11`, `0.13.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fexternal-dns)
* fluentd : [`1`, `1-debian-11`, `1.15.3`, `1.15.3-debian-11`, `1.15.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Ffluentd)
* git : [`2`, `2-debian-11`, `2.39.0`, `2.39.0-debian-11`, `2.39.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fgit)
* gitea : [`1`, `1-debian-11`, `1.17.4`, `1.17.4-debian-11`, `1.17.4-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fgitea)
* java : [`1.8`, `1.8-debian-11`, `1.8.352`, `1.8.352-debian-11`, `1.8.352-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fjava)
* java : [`11`, `11-debian-11`, `11.0.17`, `11.0.17-debian-11`, `11.0.17-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fjava)
* java : [`17`, `17-debian-11`, `17.0.5`, `17.0.5-debian-11`, `17.0.5-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fjava)
* jmx-exporter : [`0`, `0-debian-11`, `0.17.2`, `0.17.2-debian-11`, `0.17.2-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fjmx-exporter)
* kafka : [`3.1`, `3.1-debian-11`, `3.1.2`, `3.1.2-debian-11`, `3.1.2-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkafka)
* kafka : [`3.2`, `3.2-debian-11`, `3.2.3`, `3.2.3-debian-11`, `3.2.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkafka)
* kafka : [`3.3`, `3.3-debian-11`, `3.3.1`, `3.3.1-debian-11`, `3.3.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkafka)
* kafka-exporter : [`1`, `1-debian-11`, `1.6.0`, `1.6.0-debian-11`, `1.6.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkafka-exporter)
* keycloak : [`20`, `20-debian-11`, `20.0.2`, `20.0.2-debian-11`, `20.0.2-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkeycloak)
* keycloak-config-cli : [`5`, `5-debian-11`, `5.5.0`, `5.5.0-debian-11`, `5.5.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkeycloak-config-cli)
* kubectl : [`1.23`, `1.23-debian-11`, `1.23.15`, `1.23.15-debian-11`, `1.23.15-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkubectl)
* kubectl : [`1.24`, `1.24-debian-11`, `1.24.9`, `1.24.9-debian-11`, `1.24.9-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkubectl)
* kubectl : [`1.25`, `1.25-debian-11`, `1.25.5`, `1.25.5-debian-11`, `1.25.5-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fkubectl)
* mariadb : [`10.5`, `10.5-debian-11`, `10.5.18`, `10.5.18-debian-11`, `10.5.18-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb)
* mariadb : [`10.6`, `10.6-debian-11`, `10.6.11`, `10.6.11-debian-11`, `10.6.11-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb)
* mariadb : [`10.7`, `10.7-debian-11`, `10.7.7`, `10.7.7-debian-11`, `10.7.7-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb)
* mariadb : [`10.8`, `10.8-debian-11`, `10.8.6`, `10.8.6-debian-11`, `10.8.6-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb)
* mariadb-galera : [`10.5`, `10.5-debian-11`, `10.5.18`, `10.5.18-debian-11`, `10.5.18-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb-galera)
* mariadb-galera : [`10.6`, `10.6-debian-11`, `10.6.11`, `10.6.11-debian-11`, `10.6.11-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb-galera)
* mariadb-galera : [`10.7`, `10.7-debian-11`, `10.7.7`, `10.7.7-debian-11`, `10.7.7-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb-galera)
* mariadb-galera : [`10.8`, `10.8-debian-11`, `10.8.6`, `10.8.6-debian-11`, `10.8.6-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmariadb-galera)
* memcached : [`1`, `1-debian-11`, `1.6.17`, `1.6.17-debian-11`, `1.6.17-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmemcached)
* memcached-exporter : [`0`, `0-debian-11`, `0.10.0`, `0.10.0-debian-11`, `0.10.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmemcached-exporter)
* minio : [`2022`, `2022-debian-11`, `2022.12.12`, `2022.12.12-debian-11`, `2022.12.12-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fminio)
* minio-client : [`2022`, `2022-debian-11`, `2022.12.13`, `2022.12.13-debian-11`, `2022.12.13-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fminio-client)
* mongodb : [`5.0`, `5.0-debian-11`, `5.0.14`, `5.0.14-debian-11`, `5.0.14-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmongodb)
* mongodb : [`6.0`, `6.0-debian-11`, `6.0.3`, `6.0.3-debian-11`, `6.0.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmongodb)
* mongodb-exporter : [`0`, `0-debian-11`, `0.35.0`, `0.35.0-debian-11`, `0.35.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmongodb-exporter)
* mongodb-sharded : [`5.0`, `5.0-debian-11`, `5.0.14`, `5.0.14-debian-11`, `5.0.14-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmongodb-sharded)
* mongodb-sharded : [`6.0`, `6.0-debian-11`, `6.0.3`, `6.0.3-debian-11`, `6.0.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmongodb-sharded)
* mysql : [`8.0`, `8.0-debian-11`, `8.0.31`, `8.0.31-debian-11`, `8.0.31-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmysql)
* mysqld-exporter : [`0`, `0-debian-11`, `0.14.0`, `0.14.0-debian-11`, `0.14.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fmysqld-exporter)
* nginx : [`1.23`, `1.23-debian-11`, `1.23.3`, `1.23.3-debian-11`, `1.23.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fnginx)
* nginx-exporter : [`0`, `0-debian-11`, `0.11.0`, `0.11.0-debian-11`, `0.11.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fnginx-exporter)
* node : [`14`, `14-debian-11`, `14.21.2`, `14.21.2-debian-11`, `14.21.2-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fnode)
* node : [`16`, `16-debian-11`, `16.19.0`, `16.19.0-debian-11`, `16.19.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fnode)
* node : [`18`, `18-debian-11`, `18.12.1`, `18.12.1-debian-11`, `18.12.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fnode)
* openldap : [`2.6`, `2.6-debian-11`, `2.6.3`, `2.6.3-debian-11`, `2.6.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fopenldap)
* pgpool : [`4`, `4-debian-11`, `4.4.1`, `4.4.1-debian-11`, `4.4.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpgpool)
* postgres-exporter : [`0`, `0-debian-11`, `0.11.1`, `0.11.1-debian-11`, `0.11.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgres-exporter)
* postgresql : [`11`, `11-debian-11`, `11.18.0`, `11.18.0-debian-11`, `11.18.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql)
* postgresql : [`12`, `12-debian-11`, `12.13.0`, `12.13.0-debian-11`, `12.13.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql)
* postgresql : [`13`, `13-debian-11`, `13.9.0`, `13.9.0-debian-11`, `13.9.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql)
* postgresql : [`14`, `14-debian-11`, `14.6.0`, `14.6.0-debian-11`, `14.6.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql)
* postgresql : [`15`, `15-debian-11`, `15.1.0`, `15.1.0-debian-11`, `15.1.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql)
* postgresql-repmgr : [`11`, `11-debian-11`, `11.18.0`, `11.18.0-debian-11`, `11.18.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql-repmgr)
* postgresql-repmgr : [`12`, `12-debian-11`, `12.13.0`, `12.13.0-debian-11`, `12.13.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql-repmgr)
* postgresql-repmgr : [`13`, `13-debian-11`, `13.9.0`, `13.9.0-debian-11`, `13.9.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql-repmgr)
* postgresql-repmgr : [`14`, `14-debian-11`, `14.6.0`, `14.6.0-debian-11`, `14.6.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpostgresql-repmgr)
* python : [`3.10`, `3.10-debian-11`, `3.10.9`, `3.10.9-debian-11`, `3.10.9-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpython)
* python : [`3.11`, `3.11-debian-11`, `3.11.1`, `3.11.1-debian-11`, `3.11.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpython)
* python : [`3.7`, `3.7-debian-11`, `3.7.16`, `3.7.16-debian-11`, `3.7.16-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpython)
* python : [`3.8`, `3.8-debian-11`, `3.8.16`, `3.8.16-debian-11`, `3.8.16-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpython)
* python : [`3.9`, `3.9-debian-11`, `3.9.16`, `3.9.16-debian-11`, `3.9.16-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fpython)
* rabbitmq : [`3.10`, `3.10-debian-11`, `3.10.13`, `3.10.13-debian-11`, `3.10.13-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frabbitmq)
* rabbitmq : [`3.11`, `3.11-debian-11`, `3.11.5`, `3.11.5-debian-11`, `3.11.5-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frabbitmq)
* rabbitmq : [`3.9`, `3.9-debian-11`, `3.9.27`, `3.9.27-debian-11`, `3.9.27-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frabbitmq)
* rabbitmq-cluster-operator : [`2`, `2-scratch`, `2.0.0`, `2.0.0-scratch`, `2.0.0-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frabbitmq-cluster-operator)
* redis : [`6.2`, `6.2-debian-11`, `6.2.8`, `6.2.8-debian-11`, `6.2.8-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis)
* redis : [`7.0`, `7.0-debian-11`, `7.0.7`, `7.0.7-debian-11`, `7.0.7-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis)
* redis-cluster : [`6.2`, `6.2-debian-11`, `6.2.8`, `6.2.8-debian-11`, `6.2.8-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis-cluster)
* redis-cluster : [`7.0`, `7.0-debian-11`, `7.0.7`, `7.0.7-debian-11`, `7.0.7-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis-cluster)
* redis-exporter : [`1`, `1-debian-11`, `1.45.0`, `1.45.0-debian-11`, `1.45.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis-exporter)
* redis-sentinel : [`6.2`, `6.2-debian-11`, `6.2.8`, `6.2.8-debian-11`, `6.2.8-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis-sentinel)
* redis-sentinel : [`7.0`, `7.0-debian-11`, `7.0.7`, `7.0.7-debian-11`, `7.0.7-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fredis-sentinel)
* rmq-default-credential-updater : [`1`, `1-scratch`, `1.0.2`, `1.0.2-scratch`, `1.0.2-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frmq-default-credential-updater)
* rmq-messaging-topology-operator : [`1`, `1-scratch`, `1.10.0`, `1.10.0-scratch`, `1.10.0-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Frmq-messaging-topology-operator)
* sealed-secrets : [`0`, `0-scratch`, `0.19.3`, `0.19.3-scratch`, `0.19.3-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fsealed-secrets)
* solr : [`8`, `8-debian-11`, `8.11.2`, `8.11.2-debian-11`, `8.11.2-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fsolr)
* solr : [`9`, `9-debian-11`, `9.1.0`, `9.1.0-debian-11`, `9.1.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fsolr)
* thanos : [`0`, `0-scratch`, `0.29.0`, `0.29.0-scratch`, `0.29.0-scratch-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fthanos)
* tomcat : [`10.0`, `10.0-debian-11`, `10.0.27`, `10.0.27-debian-11`, `10.0.27-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Ftomcat)
* tomcat : [`8.5`, `8.5-debian-11`, `8.5.84`, `8.5.84-debian-11`, `8.5.84-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Ftomcat)
* tomcat : [`9.0`, `9.0-debian-11`, `9.0.70`, `9.0.70-debian-11`, `9.0.70-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Ftomcat)
* wordpress : [`6`, `6-debian-11`, `6.1.1`, `6.1.1-debian-11`, `6.1.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fwordpress)
* wordpress-nginx : [`6`, `6-debian-11`, `6.1.1`, `6.1.1-debian-11`, `6.1.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fwordpress-nginx)
* zookeeper : [`3.6`, `3.6-debian-11`, `3.6.3`, `3.6.3-debian-11`, `3.6.3-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fzookeeper)
* zookeeper : [`3.7`, `3.7-debian-11`, `3.7.1`, `3.7.1-debian-11`, `3.7.1-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fzookeeper)
* zookeeper : [`3.8`, `3.8-debian-11`, `3.8.0`, `3.8.0-debian-11`, `3.8.0-debian-11-r48`](https://github.com/zcube/bitnami-compat/pkgs/container/bitnami-compat%2Fzookeeper)

## Install Task

* using task https://taskfile.dev/installation/
```bash
sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin
```

## List docker image patch status

```bash
go run main.go list
```

```
...
✔️  mongodb:5.0.9
✔️  mongodb-exporter:0.33.0
❌  mysql:5.7.38
  ❌  mysql.5.7.38-150
✔️  mysql:8.0.29
✔️  mysqld-exporter:0.14.0
❌  nginx:1.21.6
  ❌  nginx.1.21.6-153
❌  nginx:1.22.0
  ❌  nginx.1.22.0-153
✔️  nginx:1.23.0
...
```

## Generate Dockerfile.arm64
```bash
task generate
```

## Upgrade bitnami docker repositories
```bash
task upgrade
```

## How to add a new image

Example : mariadb/10.8

1. Add submodule
  ```bash
  cd bitnami-dockers
  git submodule add https://github.com/bitnami/bitnami-docker-mariadb
  cd ..
  go run main.go makeDirs
  ```

2. Write build scripts
  * patches/golang/mariadb/10.8/install.sh
  * patches/docker/mariadb/10.8/Dockerfile.from
  * patches/docker/mariadb/10.8/Dockerfile.install
  * patches/bash/mariadb/10.8/install.sh

3. Apply and test
  ```bash
  task generate
  cd bitnami-dockers/bitnami-docker-mariadb/10.8/debian-11
  docker buildx create --name multiarchbuilder
  docker buildx inspect multiarchbuilder --bootstrap
  docker buildx use multiarchbuilder
  docker buildx build --platform linux/amd64,linux/arm64 -t testimage . -f Dockerfile.arm64
  docker run --rm -ti testimage
  ```

## Compatible Images for Bitnami Charts

* Recommended for production use.

|Charts|Image|Status|
|------|-----|------|
|thanos|docker.io/thanosio/thanos:v0.27.0|Official images are compatible.|
|external-dns|registry.k8s.io/external-dns/external-dns:v0.12.2|Official images are compatible.|

## License

Apache 2.0

### License for generator (Golang scripts)

MIT License

### License for patches

check References

### License for Bitnami docker scripts

Apache 2.0

## Warning

This repository contains sources, binaries and recipes from Bitnami and other software. Each software has its own license.

This repository lacks support for production use.

This repository is provided as is.

It might be dangerous for production use. Use after testing
