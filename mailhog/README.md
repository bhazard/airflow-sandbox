# Mailhog Docker Images

Mailhog provides an SMTP service for debugging apps that send email.  The
service includes a web application that allows you to see emails as they are
sent.

The application is built differently for linux and windows, so we have provided
Dockerfiles for each.  Use one or the other by specifying in your `docker-compose.yml`

For example, to use the Linux version ...

``` yaml
    mailhog:
        image: $REPONAME/mailhog
        build:
            context: ./mailhog
            dockerfile: Dockerfile.linux
        environment:
            - MH_HOSTNAME=mailhog  # Should match the service name
        # Access the mailhog web interface from the host on http://localhost:8025
        ports:
            - 8025:8025

```

Note that containers within the same docker network should be configured to 
send mail to the above service by connecting to port 1025 (default port) on host 
mailhog.


## Windows

The windows image is not used by the airflow sandbox, but is included here lest
it is helpful.  It could be used in a windows-hosted docker environment where
all containers are windows.
