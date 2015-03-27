# Running a Production Stack Locally
Let's take a look at how to replicate a production stack locally so that we can easily debug and replicate a production like environment on our dev machines.

At this point you should have boot2docker installed and up and access to the docker hub repos.

## Docker and Consul
[consul-docker](https://github.com/ridecharge/consul-docker)

Consul is a configuration service that exposes a key/value store over http.  It'll be a key part of our Service Oriented Architecture.

First we need to run a docker container that sets up consul in a single cluster mode.  

```shell
docker run -e LOCAL_MODE=true -p 8500:8500 --name consul ridecharge/consul
```

The above command takes a environment variable `LOCAL_MODE` that will configure our consul service to run a single node instead of the full cluster in EC2.

`p -8500:8500` will expose port 8500 on the host to allow for easy access to populate data in the store.

Remember that we are running boot2docker so if you wanted to curl the consul end point it would be 
```shell
curl `boot2docker ip`:8500
```

## curbd
Next we need to populate the configuration data in consul. We've developed a tool [curbd](https://github.com/ridecharge/curbd) which will take json from the project folders and issue PUTs to the consul service.

This point we are assuming you already have installed python3 and have curbd installed by pip.

```shell
curbd from_cf --host `boot2docker ip` --port 8500 --environment mock env
curbd from_cf --host `boot2docker ip` --port 8500 --environment mock cfversions
```

This command typically calls out to CloudFormation and describes the stack who's name is a combination of the `--environment` and the positional parameter (so `test-env` and `test-cfversions` in this case).  But we've also passed the `--local` flag which allows us populate from a json file mocking the CF API data, and ignoring the `--environment` flag.  

After this command runs we should have values under the key `cf/env/` which is our keyspace `cf` which matches up with cf tool and `env` which is the stack template our outputs are from. 

You can verify this by running 
```shell
curl `boot2docker ip`:8500/v1/kv/cf/env/environment
```
which should be `test` base64 encoded in the `Value` property in the json response.

Note that with proper access in AWS you could populate the values from the live stack and run in a fully production mode locally which can be useful but also do so with caution.

Next let's see how we populate program configuration from json.

```shell
curbd from_json --host `boot2docker ip` --port 8500 nginx cfversions
```

In this case we use the `from_json` subcommand.  This command takes two positional parameters.  First the program name, which will match up with a directory `curbd/nginx`, and a service name, which will map up to `curbd/nginx/cfversions.json` file.

These json files are flat and will populate the keyspace `nginx/cfversions/`

## cfversions service
[cfversions](https://github.com/ridecharge/cfversions)

This is a simple service written in golang to query the configuration data populated by `curbd`.  It expose a json endpoint to view a services deployed Version and other properties.

```shell
docker run --link consul:consul --name cfversions ridecharge/cfversions
```

`--link consul:consul --link name:alias` will expose the alias as a hostname in our container. 

If you aren't planning on going past this point and setting up nginx as well then you may want to add `-p 8080:8080` to expose the application to the docker host so you can access it.

The app then uses the hostname from the linked container 

```go
host := flag.String("consul-host", "consul", "the consul host")
port := flag.String("consul-port", "8500", "the consul port")
scheme := flag.String("consul-scheme", "http", "the consul port")
flag.Parse()
consulConfig = &api.Config{
	Address: *host + ":" + *port,
	Scheme:  *scheme}
client, err := api.NewClient(consulConfig)
```

## logging-docker
This container runs a syslog server. This isn't very important for local machines, but nginx will complain if its not linked against a logging container since it is configured to use syslog. We'll go ahead and insert a fake token for loggly so we don't index the logs.

```shell
docker run -d --restart=always --name logging -e ENVIRONMENT=test -e LOGGLY_TOKEN=abc ridecharge/logging:latest
```

## nginx-docker
[nginx-docker](https://github.com/ridecharge/nginx-docker)

The last thing running in our stack is the nginx docker container.

```shell
docker run -p 8080:8080 -e SERVICE_NAME=cfversions --link logging:logging --link consul:consul --link cfversions:app --name nginx ridecharge/nginx 
```
Here we link against the `consul` and `cfversions` containers. You'll notice that we alias the cfversions link as `app` which allows us to refer to it in our nginx configuration as `app` for the backend.

Also here we pass in a `SERVICE_NAME` environment variable to use in our consul key lookup.

If you remember before we populated some nginx configuration in consul.  We use a program called `confd` which allows us to watch keys in consul and automatically process a template to configure the program.
