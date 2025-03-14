# Getting started

`fcct`, the Fedora CoreOS Config Transpiler, is a tool that consumes a Fedora CoreOS Config and produces an Ignition config, which is a JSON document that can be given to a Fedora CoreOS machine when it first boots. Using this config, a machine can be told to create users, create filesystems, set up the network, install systemd units, and more.

Fedora CoreOS Configs are YAML files conforming to `fcct`'s schema. For more information on the schema, take a look at [doc/configuration-v1_0.md][spec].

### Getting FCCT

Download the latest version of `fcct` and the detached signature from the [releases page](https://github.com/coreos/fcct/releases). Verify it with gpg:

```
gpg --verify <detached sig> <fcct binary>
```
You may need to download the [CoreOS Application Signing Key](http://coreos.com/security/app-signing-key/) and import it with `gpg --import <key>` if you have not already done so.

New releases of `fcct` are backwards compatible with old releases unless otherwise noted.

### Writing and using Fedora CoreOS Configs

As a simple example, let's use `fcct` to set the authorized ssh key for the `core` user on a Fedora CoreOS machine.

```yaml fedora-coreos-config
variant: fcos
version: 1.0.0
passwd:
  users:
    - name: core
      ssh_authorized_keys:
        - ssh-rsa AAAAB3NzaC1yc...
```

In this above file, you'll want to set the `ssh-rsa AAAAB3NzaC1yc...` line to be your ssh public key (which is probably the contents of `~/.ssh/id_rsa.pub`, if you're on Linux).

If we take this file and give it to `fcct`:

```
$ ./bin/amd64/fcct --input example.yaml

{"ignition":{"config":{"replace":{"source":null,"verification":{}}},"security":{"tls":{}},"timeouts":{},"version":"3.0.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa ssh-rsa AAAAB3NzaC1yc..."]}]},"storage":{},"systemd":{}}
```

We can see that it produces a JSON file. This file isn't intended to be human-friendly, and will definitely be a pain to read/edit (especially if you have multi-line things like systemd units). Luckily, you shouldn't have to care about this file! Just provide it to a booting Fedora CoreOS machine and [Ignition][ignition], the utility inside of Fedora CoreOS that receives this file, will know what to do with it.

The method by which this file is provided to a Fedora CoreOS machine depends on the environment in which the machine is running. For instructions on a given provider, head over to the [list of supported platforms for Ignition][supported-platforms].

To see some examples for what else `fcct` can do, head over to the [examples][examples].

[spec]: configuration-v1_0.md
[ignition]: https://github.com/coreos/ignition
[supported-platforms]: https://github.com/coreos/ignition/blob/master/doc/supported-platforms.md
[examples]: examples.md
