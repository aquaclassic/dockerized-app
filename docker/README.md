Make the build executable

```
chmod +x build

```

Context and instructions are set in docker-compose[base|dev]
default => this is nginx configuration that will be mounted and replaced, it is
customize for the docker needs

Dockerfile => building image for this app

php-fpm.conf => its instructing php to run in foreground and to change logging
to /proc/self/fd/2 which is the same as stdout/stderr.
/dev/stderr is ln -s for /proc/self/fd/2 -> for docker to use those logs

supervisor.conf => will manage the processes and run them, it run in foreground.
