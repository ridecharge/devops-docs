# Running a Production Stack Locally
Let's take a look at how to replicate a production stack locally so that we can easily debug and replicate a production like environment on our dev machines.

At this point you should have boot2docker installed and up and access to the docker hub repos.

## Running Consul
First we need to setup consul in a single cluster mode.  Consul is a configuration service that exposes a key/value store over http.  It'll be a key part of our Service Oriented Architecture.

```shell
docker run -e LOCAL_MODE=true -p 8500:8500 ridecharge/consul
```

The above command takes a environment variable `LOCAL_MODE` that will configure our consul service to run a single node instead of the full cluster in EC2.

`p -8500:8500` will expose port 8500 on the host to allow for easy access to populate data in the store.

Remember that we are running boot2docker so if you wanted to curl the consul end point it would be 
```shell
curl `boot2docker ip`:8500/path
```

### Populating Consul
Next we need to populate the configuration data in consul. We've developed a tool [curbd](https://github.com/ridecharge/curbd) which will take json from the project folders and issue PUTs to the consul service.

This point we are assuming you already have installed python3 and have curbd installed by pip.

```shell
curbd from_cf --host `boot2docker ip` --port 8500 --environment test --local env
```

This command typically calls out to CloudFormation and describes the stack who's name is a combination of the `--environment` and the positional parameter (so `test-env` in this case).  But we've also passed the `--local` flag which allows us populate from a json file mocking the CF API data.  

After this command runs we should have values under the key `cf/env/` which is our namespace `cf` which matches up with cf tool and `env` which is the template our outputs are from.

You can verify this by running 
```shell
curl `boot2docker ip`:8500/v1/kv/cf/env/environment
```
which should be `test` base64 encoded in the `Value` property in the json response.


