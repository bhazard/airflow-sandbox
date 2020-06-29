# airflow-sandbox
A multi-container docker sandbox for [Apache Airflow](https://airflow.apache.org/), 
including batch and streaming via kafka with development tools.

The purpose of this project is to provide a sandbox environment to explore how
Apache Airflow works.  The sandbox includes developer tools as well as common 
production tools. This first version of the sandbox is meant to run locally
via `docker-compose`; however, other deployment options will be added.

This project creates separate containers for:

Core Airflow 

- airflow-ui -- Airflow's web UI
- postgres -- persistent data store for airflow's metadata
- scheduler / executor -- [Celery](https://github.com/celery/celery) is used as the Executor (alternatives include a LocalExecutor or MesosExecutor)
- worker -- Celery worker(s)
- redis -- Message broker for Celery (Celery could also use RabbitMQ or Amazon SQS)

Optional Development / Monitoring Tools

- [redisinsight](https://redislabs.com/redisinsight/) -- a web ui for viewing redis status
- [flower](https://github.com/mher/flower) -- web and api for monitoring Celery clusters
- [mailhog](https://github.com/mailhog/MailHog) -- web email testing tool
- [adminer](https://www.adminer.org/) -- web database browser

Future Work 

- kafka -- streaming example
- zookeeper -- for kafka
- log gatherer / monitor

## Quickstart

This project is built and maintained on an mac (intel, in case that matters to a 
future unfortunate reader) ... ymmv if you run it elsewhere (but happy to accept
PRs if that helps)

- clone this repo
- copy `env.template` to `.env` and edit as needed
- `docker-compose up`
- URL's (unless you edit ports in `.env`)
  - [Airflow UI](http://localhost:8080)
  - [Adminer](http://localhost:8087)
  - [Flower](http://localhost:5555)
  - [Redis Insight](http://localhost:8085)
  - [MailHog](http://localhost:5555)

### Setup

### Start

`docker-compose up --scale worker=2`

`docker-compose down -v`

### Notes

Base Image ... why we care

Access from airflow commandline
Access to flower API

## Overview

### Components

## Setup / Configuration


# To-dos


- [ ] Autoconfigure `redisinsight` so that it finds the configured redis server
- [ ] Autoconfigure `adminer` so that it sees the postgres db
- [ ] Add Kafka streaming
- [ ] Kubernetes deployment
- [ ] OpenFaaS to host endpoints for task implementations
