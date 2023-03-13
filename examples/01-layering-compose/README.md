# Supporting multiple environments in Docker Compose (optional content, will not be used in labs)

One of the goals of Docker is the "build once, run anywhere" consistency it provides.  Docker
images let you create containers that are virtual duplicates of each other, so what happens in dev
can be the same thing as what happens in prod.

This is indeed useful and desirable, however there are always ways you _want_ your dev environment
to be different from your prod environment.  For example, you likely don't want your dev
environment to be customer-facing.  You might also use a different database server instance, or
even entirely different database server software.  Log retention is different, email processing,
access control, and the list goes on.

For these instances where you want the software to be the same, the environment the software runs
in to be the same, but the configuration to be different, Docker Compose has a facility that lets
you separate environment-specific configuration from generic configuration and service definition.
The facility is called layering, and services can "extend" service definitions contained in other
yml layers.

For instance, let's start with the simple example contained herein.  First, build your example:
```
[jschmersal1@WFIL011 01-layering-compose]$ ./build.sh 
Sending build context to Docker daemon  30.72kB
Step 1/4 : FROM busybox
 ---> 018c9d7b792b
Step 2/4 : ENV DEPLOYED_ENVIRONMENT ""
 ---> Using cache
 ---> 9bd32c7fa0ef
Step 3/4 : COPY * app/
 ---> 0d5923f7fa14
Step 4/4 : CMD /app/run-it.sh
 ---> Running in c98268fbbe36
Removing intermediate container c98268fbbe36
 ---> 05560cf01c55
Successfully built 05560cf01c55
Successfully tagged infra-example-4-01:0.1
```

Next, start up the basic composition:
```
[jschmersal1@WFIL011 01-layering-compose]$ docker-compose up -d
Creating 01-layering-compose_app_1 ... done
[jschmersal1@WFIL011 01-layering-compose]$ docker ps
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS               NAMES
1fae2f7e7dd5        infra-example-4-01:0.1   "/bin/sh -c /app/run…"   8 seconds ago       Up 8 seconds                            01-layering-compose_app_1
```

If you inspect the [Dockerfile](Dockerfile) you'll see that this image expects an environment
variable, `DEPLOYED_ENVIRONMENT` to be defined.  The basic [docker-compose.yml](docker-compose.yml)
has that environment variable set to "none".  You can see that by inspecting the docker logs for
the running container:
```
[jschmersal1@WFIL011 01-layering-compose]$ docker logs --tail=1 01-layering-compose_app_1 
At Tue Aug  4 19:14:32 UTC 2020 the current environment is: none
```

Let's say we want to augment this basic `docker-compose.yml` with per-environment configuration,
but want to keep lots of the same basic service configuration (in this example it's just the one
service (`app`) and the only common configuration is the image (`infra-example-4-01:0.1`), but in
an enterprise application there are usually a large number of configuration bits to tweak
(including more than just environment variables - for instance, volume definitions).

To do so, `docker-compose` lets you pass a list of yml files that make up the composition.  Check
out [compose-prod.yml](compose-prod.yml).  Notice how it's redefining the `DEPLOYED_ENVIRONMENT`
variable.  To see this in action:

* First stop your existing application (`docker-compose down`)
* Next, start the new application instance: `docker-compose -f docker-compose.yml -f compose-prod.yml up -d`
```
[jschmersal1@WFIL011 01-layering-compose]$ docker-compose -f docker-compose.yml -f compose-prod.yml up -d
Creating 01-layering-compose_app_1 ... done
```
* Finally, re-run your `docker logs` command and observe the changed output:
```
[jschmersal1@WFIL011 01-layering-compose]$ docker logs --tail=1 01-layering-compose_app_1 
At Tue Aug  4 19:27:15 UTC 2020 the current environment is: PROD
```

As you can see, layering compose files can give you a bulk of common service configuration while
still allowing per-environment configuration.

Question:  What happens if you reverse the order of the compose files you pass with the `-f` option
of `docker-compose`?
