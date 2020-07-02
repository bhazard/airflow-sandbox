# airflow docker images

Seveal of the dodker images for this project include airflow itself.  These are built in the directories below.

- ui -- houses the airflow web interface
- scheduler -- houses the scheduler
- worker -- houses the worker

Note that the original Dockerfiles and entrypoint files were derived from those
included in the excellent [docker-airflow](https://github.com/puckel/docker-airflow) project maintained by Matthieu Roisil.  With thanks to Matthiew to sorting that
all out.  Because that project had one image that was used for each role, the
images created by the Dockerfiles here very likely include more than they might
require.  The entrypoint files, in particular, can
be simplified based on more narrow roles of the images.  We will optimize over time.

Because this project takes a different approach from that taken in docker-airflow,
we should make some further optimizations:

- [ ] Simplify the entrypoint files
- [ ] Use smaller / optimized base images
- [ ] Don't install un-needed tools (based on role)
- [ ] Limit pip installs based on role
- [ ] Ensure healh checks for each service
- [ ] Organize dockerfiles to ensure that layers respect roles defined above (e.g., that base stuff required by all images is installed first in a separate layer)

