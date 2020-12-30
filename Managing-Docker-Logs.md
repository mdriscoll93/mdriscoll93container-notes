# Managing Docker Logs 

> This scenario will explore the different ways you can handle logging output from your application and services when running as containers.

## Step 1 - Docker Logs

When you start a container, Docker will track the Standard Out and Standard Error outputs from the process and make them available via the client.

### Example

In the background, there is an instance of Redis running with the name redis-server. Using the Docker client, we can access the standard out and standard error outputs using `docker logs redis-server`

## Step 2 - SysLog

By default, the Docker logs are outputting using the json-file logger meaning the output stored in a JSON file on the host. This can result in large files filling the disk. As a result, you can change the log driver to move to a different destination.

### Syslog

The Syslog log driver will write all the container logs to the central syslog on the host. "syslog is a widely used standard for message logging. It permits separation of the software that generates messages, the system that stores them, and the software that reports and analyses them." Wikipedia

This log-driver is designed to be used when syslog is being collected and aggregated by an external system.

### Example

The command below will redirect the redis logs to syslog.

`docker run -d --name redis-syslog --log-driver=syslog redis`

### Accessing Logs

If you attempted to view the logs using the client you'll receive the error FATAL[0000] "logs" command is supported only for "json-file" logging driver

Instead, you need to access them via the syslog stream.

## Step 3 - Disable Logging

The third option is to disable logging on the container. This is particularly useful for containers which are very verbose in their logging.

### Example

When the container is launched simply set the log-driver to none. No output will be logged.

`docker run -d --name redis-none --log-driver=none redis`

### Which Config?

The inspect command allows you to identify the logging configuration for a particular container. The command below will output the LogConfig section for each of the containers.

Server created in step 1
`docker inspect --format '{{ .HostConfig.LogConfig }}' redis-server`

Server created in step 2
`docker inspect --format '{{ .HostConfig.LogConfig }}' redis-syslog`

Server created in this step
`docker inspect --format '{{ .HostConfig.LogConfig }}' redis-none`

## Console Output

```Bash
$ docker run -d --name redis-server redis
2e51f3455d60eec53f7938e27e454ffc16b160bf1118852f82a214c16da1e5df
$
$
$ docker logs redis-server
1:C 30 Dec 2020 08:34:11.378 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 30 Dec 2020 08:34:11.383 # Redis version=6.0.9, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 30 Dec 2020 08:34:11.383 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 30 Dec 2020 08:34:11.385 * Running mode=standalone, port=6379.
1:M 30 Dec 2020 08:34:11.385 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 30 Dec 2020 08:34:11.385 # Server initialized
1:M 30 Dec 2020 08:34:11.385 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:M 30 Dec 2020 08:34:11.385 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo madvise > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled (set to 'madvise' or 'never').
1:M 30 Dec 2020 08:34:11.385 * Ready to accept connections
$ docker run -d --name redis-syslog --log-driver=syslog redis
be16f115b7f5a48e18da9bf791864c749973cd788a3381e3d00f75c37cf09019
$ docker run -d --name redis-none --log-driver=none redis
95b78678e68227438382f521120767d03240a76aa40b110050a40798a76db059
$ docker inspect --format '{{ .HostConfig.LogConfig }}' redis-server
{json-file map[]}
$ docker inspect --format '{{ .HostConfig.LogConfig }}' redis-syslog
{syslog map[]}
$ docker inspect --format '{{ .HostConfig.LogConfig }}' redis-none
{none map[]}
$ ^C
```