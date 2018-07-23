# delve-release

Bosh release packaging [delve](https://github.com/derekparker/delve), Delve is a debugger for the Go programming language.

### Usage

1. Upload release `bosh upload-release https://github.com/carlo-colombo/delve-release/releases/download/v1/delve-release-1.tgz`
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
