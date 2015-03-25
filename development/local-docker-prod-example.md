# Running a Production Stack Locally
Let's take a look at how to replicate a production stack locally so that we can easily debug and replicate a production like environment on our dev machines.

At this point you should have boot2docker installed and up and access to the docker hub repos.

## Running Consul
First we need to setup consul in a single cluster mode.  Consul is a configuration service that exposed a key/value store over http.  It'll be a key part of our Service Oriented Architecture.

```shell
docker run -e LOCAL_MODE=true -p 8500:8500 ridecharge/consul
```

The above command takes a environment variable `LOCAL_MODE` that will configure our consul service to run a single node instead of the full cluster in EC2.

`p -8500:8500` will expose port 8500 on the host to allow for easy access to populate data in the store.

Remember that we are running boot2docker so if you wanted to curl the consul end point it would be 
```shell
curl `boot2docker ip`:8500/path`
```