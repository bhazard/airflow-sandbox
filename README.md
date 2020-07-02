# airflow-sandbox

A multi-container docker sandbox for [Apache Airflow](https://airflow.apache.org/).

The purpose of this project is to provide a sandbox environment to explore how
Apache Airflow works.  The sandbox consists of a set of containers, organized as
separate services.  The services include developer tools as well as common 
production tools. 

Before continuing, please note that there is an excellent and very mature Docker container option that uses
one image and spawns it with different roles.  That project is maintained by
Matthieu Roisil and is the base for many popular articles on Airflow and Docker
across the web.  Please see [docker-airflow](https://github.com/puckel/docker-airflow)
for details.

Please note that this project is *not* intended to be a way for a beginnger to
get started with airflow.  For that, please see the all-in-one image referenced above,
as it is very likely the solution referenced in any getting started articles.  The
images created here are more folks that have done the basic tutorials, and are ready
to roll up their sleeves and writee some real DAGS.  In that spirit, we won't try
to explain much in the way of airflow basics, except as they relate to how we have
organized these images and their interconnections.

## Image Overview

This project creates separate images with specific service roles as described below.
There are a number of alternatives for setting up airflow.  Here, we are using
Celery as the executor and it uses redis at its message broker.

### Core Airflow 

The main (required) images / containers that make up airflow are as follows.

- airflow-ui -- Airflow's web UI
- postgres -- persistent data store for airflow's metadata
- scheduler / executor -- [Celery](https://github.com/celery/celery) is used as the Executor (alternatives include a LocalExecutor or MesosExecutor)
- worker -- Celery worker(s)
- redis -- Message broker for Celery (Celery could also use RabbitMQ or Amazon SQS)

### Optional Development / Monitoring Tools

The images listed here are all optional, but may help you explore the workings of
airflow.

- [redisinsight](https://redislabs.com/redisinsight/) -- a web ui for viewing redis status
- [flower](https://github.com/mher/flower) -- web and api for monitoring Celery clusters
- [mailhog](https://github.com/mailhog/MailHog) -- web email testing tool
- [adminer](https://www.adminer.org/) -- web database browser

## Quickstart

The following steps should get you up and running.  The project is built and
maintained on a mac, so if you do not happen to be on a mac, things may not go as
planned.  If you have any issues (mac or not), please do LMK via github issues.

I believe the only pre-requisites here are `git` and `docker`, but LMK if I am
missing something.

Here we go:

- install docker
- clone this repo
- copy `env.template` to `.env` and edit as needed
- `docker-compose build`
- `docker-compose up --scale worker=2`
- URL's (unless you edit ports in `.env`)
  - [Airflow UI](http://localhost:8080)
  - [Adminer](http://localhost:8087)
  - [Flower](http://localhost:5555)
  - [Redis Insight](http://localhost:8085)
  - [MailHog](http://localhost:8025)

## Deriving from Here

If your interest is more about developing DAGS (that is my motiviation) than playing
with airflow itself, then you may find it useful to clone this project, edit `.env` and build the images.  THen use the built images from a separate project.

Create a new project from there and copy the `docker-compose.yml` and `.env` into that new project.  Remove the build directives from the `.yml` file.  Create your own, project-specific dag and plugins directories.  Docker-compose will fire up the containers described here for use in your new project.

## How To ...

Since I created this sandbox to enable experimentation with Airflow, here are a
few experiements that one could perform.

### Test that Things are Working

After running `docker-compose up`, you can verify that the system is functioning by
performing some of the tests described here.

`docker-compose ps` should show 9 running images.

Visit: http://localhost:8080/health to see a json document showing the helath of the scheduler.

Now, give the UI a whirl by visiting http://localhost:8080.  There you will see 
a DAG called tutorial (as well as a couple of other sample DAGs).

Flower will be at http://localhost:5555 (assuming you did not disable it).  It
should show you that you have one celery worker.  If there are no workers, check 
for errors in the flower_1 and worker_1 logfiles.

Redisinsight (at http://localhost:8085) will show that it has connected to the 
redis server, and that there is no activity.

When you're ready, return to the Airflow UI, and click the "on" switch next to
the Tutorial DAG.  This will execute the DAG 2 times (each will run 3 tasks).  You 
can see Airflow's view of the world in the Airflow UI by clicking on the Tutorial link on the DAG page.

Once the DAG is running (it will only run for less than a minute or so), you can see the
tasks queued in flower.  It will queue and execute 6 tasks (3 for each run) 
to complete the tutorial run.

As celery talks to its workers, it will queue redis messages, so redisinsight will
now show some activity as well.

Once a task has completed, click on that task and view the logfile.  This will
ensure that the scheduler can talk to the worker on port 8793 to retrieve the
log from the worker.

If a DAG fails to run, the scheduler will send an email.  Verify this functionality
by enabling the "fail" DAG.  Fire up mailhog's web UI (at http://localhost:8025)
to view the email that was sent by the scheduler.  The email will contain a link
to the relevant log file.  Click to see what this looks like.

And you're off and running!

### Add a DAG

The local dags directory is mounted as a volume on all relevant containers, so
any py files you put there with the appropriate classes will show up in airflow.
Note that, because the directory is mounted, you can change the py files on the
fly and the running airflow containers will use the latest version when next
you execute the DAG.

### Add a DAG with Modules

The easiest way to add custom modules is as a subdirectory to the dags dir.  Create
whatever structure you like, and `import` whatever you may need into your .py
files.

### Add a DAG with Dependencies

OK, but what if I need to pip install something, like pandas?  The workers will
each need to have that lib available to them.  For this, the worker image entrypoint
script looks for a worker-requirements.txt file in the dags dir.  This file is
read *only* on container start, and uses `pip` to install the content.  If you
modify this file, be sure to restart the workers.

### Add Workers

This one is easy, thanks to `docker-compose`:

`docker-compose up --scale worker=2`

Verify that celery shows you 2 workers.  


# To-dos

An incomplete list of to-dos, in no particular order.

- [ ] Move todos to github issues  :/
- [ ] Optimize the images for the individual airflow components
- [ ] Ensure that if an env var is not set (or `.env` does not exist), we let folks know about it or provide defaults.
- [ ] Configure Redis Result backend
- [ ] Do something about automated testing of the running environment (other than health checks)
- [ ] Separate out "core" `requirements.txt` files for the services.
- [ ] Enable plugins
- [ ] Create a Redis Sentinel Cluster
- [ ] Add healthchecks for all containers
- [ ] Make all connections encrypted (TLS)
- [ ] Add auth to all connections
- [ ] Enable TLS on the airflow UI and API
- [ ] Add authentication to the airflow UI and API
- [ ] Enable TLS for flower UI and API
- [ ] Central logging
- [ ] Alerts on Fails (other than via scheduler emails)
- [ ] Central monitoring (via prometheus or similar)
- [ ] Autoconfigure `redisinsight` so that it finds the configured redis server
- [ ] Autoconfigure `adminer` so that it sees the postgres db
- [ ] Add Kafka streaming (with zookeeper, probably)
- [ ] Kubernetes deployment
- [ ] Add a KubernetesExecutor
- [ ] OpenFaaS to host endpoints for task implementations
- [ ] Other python versions
- [ ] Windows (maybe it works? ... nah, probably not)
- [ ] Add a simple web app/page to host the tool links?
- [ ] Configure logging for mailhog (disabled right now -- can we enable)
- [ ] Convert to alpine (or similar) for airflow services

# Bugs / Issues

Please report issues / questions on the github issue tracker for this project.

# Contributing

PR's welcome.

As this is a sandbox, I'd love to include any contributions that will help others
learn about airflow.  In particular, there are many other options for configuring
airflow, such as using other executors or configuring Celery to use a different
broker.  And example dags of course.

# Reference Materials

[ETL Best Practices with Airflow 1.8](https://gtoonstra.github.io/etl-with-airflow/index.html)

