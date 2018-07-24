# delve-release

Bosh release packaging [delve](https://github.com/derekparker/delve), Delve is a debugger for the Go programming language.

### Usage

1. Upload release `bosh upload-release https://github.com/carlo-colombo/delve-release/releases/download/v2/delve-2.tgz`
2. Add job to the instance group where you want to debug a binary

```
- type: replace
  path: /releases/-
  value:
    name: delve
    version: latest
- type: replace
  path: /instance_groups/0/jobs/-
  value:
    name: delve
    release: delve
```

3. Delve is available at the location `/var/vcap/packages/delve/bin/dlv` check [delve documentation](https://github.com/derekparker/delve/blob/master/Documentation/usage/README.md)

### Debug a service adapter

1. Upload release `bosh upload-release https://github.com/carlo-colombo/delve-release/releases/download/v2/delve-2.tgz`
2. Configure the broker to execute the job `delve-exec-headless`
3. Configure the `delve-exec-headless` to execute the original adapter

```
- type: replace
  path: /instance_groups/0/jobs/-
  value:
    name: delve-exec-headless
    release: delve
    properties:
      delve:
        binary: /var/vcap/packages/odb-service-adapter/bin/service-adapter
        binary_args: []  # some adapter may require arguments to be run
        port: 7654 # 7654 is the default port
- type: replace
  path: /instance_groups/0/jobs/name=broker/properties/service_adapter/path
  value: /var/vcap/jobs/delve-exec-headless/bin/run
```

4. Trigger the execution of the adapter (eg `cf create-service ...`)
5. Execute `dlv connect <broker-ip>:7654`.

### Show the source code
Unless you are compiling locally your binary, and running `dlv` from that directory some fiddling is necessary to show the code.

1. Run `dlv connect <broker-ip>:7654`
2. Set a breakpoint for example at `main.main` (`breakpoint main.main`)
3. `continue`
4. `list`, observe the path that fail to load
5. `exit`
6. Run `docker run -it -v $(pwd):<compiled-binary-path>  delve  /dlv connect <broker-ip>:7654` from the source of your binary. You need to match paths eg `-v /Users/pivotal/workspace/flex-carlo/kafka-example-service-adapter-release:/var/vcap/packages/odb-service-adapter`

