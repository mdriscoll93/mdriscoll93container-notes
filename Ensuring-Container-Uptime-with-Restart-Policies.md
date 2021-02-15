# Ensuring Container Uptime

> In the previous scenarios we've launched some containers, but like any process, containers can crash. This scenario will explore how you can keep containers live and automatically restart them if the crash unexpectedly.

---

# Step 1 - Stop on Fail

## Example

We've created a special container which outputs a message and then exts with code 1 to simulatea a crash. Run the following command to launch the instance: `docker run -d --name restart-default scrapbook/docker-restart-`

If you list all the containers, including stopped, you will see the container has crashed `docker ps -a`

While the logs will output our message, which in real-life would hopefully indicate information to help us diagnose the issue.
`docker logs restart-default`

### Terminal Output

```bash
$ docker run -d --name restart-default scrapbook/docker-restart-example
6df8255dbf3a4598a7c5eadd2ea133d0e1beec5de3ffd2e8eb40e1ff0c4ed978
$ docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS                     PORTS               NAMES
6df8255dbf3a        scrapbook/docker-restart-example   "/bin/sh -c ./launch…"   7 seconds ago       Exited (1) 5 seconds ago                       restart-default
$ docker logs restart-default
Sun Feb 14 12:01:40 UTC 2021 Booting up...
$ docker logs restart-default
Sun Feb 14 12:01:40 UTC 2021 Booting up...
$ docker logs restart-default
Sun Feb 14 12:01:40 UTC 2021 Booting up...
$ 
```

# Restart on Fail

The option --restart=on-failure:# allows you to say how many times Docker should try again. In the example below, Docker will restart the container three times before stopping.
`docker run -d --name restart-3 --restart=on-failure:3 scrapbook/docker-restart-example)`

## Example

The option `--restart=on-failure:# --restart=on-failure:3 