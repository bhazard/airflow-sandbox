# airflow-sandbox
A multi-container docker sandbox for [Apache Airflow](https://airflow.apache.org/), 
including batch and streaming via kafka with development tools.

The purpose of this project is to provide a sandbox environment to explore various 
interactions with Airflow.  The sandbox includes developer tools as well as common 
production tools. The sandbox may be run locally, or in a Kubernetes cluster.  

This project creates separate containers for:

- airflowui -- Airflow's web UI
- postgres -- persistent data store for airflow's metadata
- scheduler / executor -- [Celery](https://github.com/celery/celery) is used as the Executor (alternatives include a LocalExecutor or MesosExecutor)
- worker -- Celery worker(s)
- redis -- Message broker for Celery (Celery could also use RabbitMQ or Amazon SQS)
- [flower](https://github.com/mher/flower) -- web and api for monitoring Celery clusters
- [mailhog](https://github.com/mailhog/MailHog) -- web email testing tool
- db browser thingy
- log gatherer thingy / prometheus?
- kafka
- zookeeper

## Quickstart

This project is built and maintained on an mac (intel, in case that matters to a 
future unfortunate reader) ... ymmv if you run it elsewhere (but happy to accept
PRs if that helps)

- clone this repo
- copy `env.template` to `.env` and edit as needed
- `docker-compose up`
- URL's (unless you edit ports in `.env`)
  - [Airflow UI](http://localhost:8080)
  - [Flower](http://localhost:5555)
  - [MailHog](http://localhost:5555)


### Setup

### Start

### Notes

Access from airflow commandline
Access to flower API

## Overview

### Components

## Setup / Configuration


# To-dos

[] - Add Kafka streaming
[] - Kubernetes deployment
[] - OpenFaaS / P
