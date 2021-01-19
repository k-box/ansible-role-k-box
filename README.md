# K-Box

The K-Box is able to set up containerized K-Boxes on the host system. It
supports K-Box version 0.20.0 to 0.32.x.

This role also provides a way to upgrade to newer versions.
Downgrading back to older versions is not supported.

> `master` uses Traefik version 2.x, if you are still on 
Traefik 1.x use [`traefik-1.x` branch](https://github.com/k-box/ansible-role-k-box/tree/traefik-1.x)

## Requirements

A working [docker](https://github.com/OneOffTech/ansible-role-docker) 
and [Traefik reverseproxy](https://github.com/OneOffTech/ansible-role-reverseproxy) installation.

## Configuration Variables
```yaml
k_boxes: # array that contains all the k-boxes, per host.
  # the domain is automatically used for the reverse proxy, can be changed
  # after setup
  domain.example.org:
    # location of the docker-compose service, must not be changed
    # after deploying
    path: "/home/user/deploy/..."
    # location of data dirs (created automatically), changing after setup
    # results in empty instance. copy data to this location prior to
    # running the playbook, in order to keep your data
    data: "/data/k-box/domain-example-org"
    # optional password for mysql service
    mysql_pw: "foobar"

```

## Example configuration
```yaml
k_boxes:
  # for each k-box an entry is defined, the domain name is the key
  new.kbox.net:
    path: "/home/user/deploy/k-box/new-kbox-net"
    data: "/data/k-box/new-kbox-net"
    # to migrate old deployments, the old mysql pw can be supplied
    mysql_pw: "hunter1"
    images:
      k_box: "klinktech/k-box:0.32.1"
      k_search: "klinktech/k-search:3.6.0-2"
      solr: "klinktech/k-search-engine:1.0.1-1"

  # multiple k-boxes can be defined per host
  old.kbox.net:
    path: "/home/user/deploy/k-box/old-kbox-net"
    data: "/data/k-box/old-kbox-net"
    images:
      k_box: "klinktech/k-box:0.27.2"
      k_search: "klinktech/k-search:3.6.0-2"
      solr: "klinktech/k-search-engine:1.0.1-1"

```

## Upgrading installations

To Upgrade installations it is sufficient to simply increment the version
Numbers and run the playbook again.

Make sure the K-Box, K-Search and K-Search-Engine versions are compatible.

Please note that you also need to re-index all documents on your K-Box when
either upgrading from 0.19 to 0.20, or 0.20 to 0.21.

## Testing

Ansible role testing is done via [Molecule](https://molecule.readthedocs.io/en/latest/index.html).

To prevent unattented changes to your environment, tests are run using 
Docker under Debian 10 (buster).

```bash
docker run --rm -it \
  -v "$(pwd)":/molecule/kbox/ \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -w /molecule/kobx/ \
  quay.io/ansible/molecule:3.1.5 \
  molecule {command}
```

> We assume that you are running your command on a Linux based OS or under 
the Windows Subsystem for Linux (WSL)

> **Next sections assume that you'll run the commands within Docker and** 
**shows only the corresponding molecule command parameters**


### Lint files

Linting ensures that the syntax is correct. 

You can run the linter by executing

```bash
molecule lint
```

### Execute tests

Automated tests tries to ensure that changes do not introduce regressions and bugs.
Although are executed in a production like scenario they can't be considered the
only way the project is tested.

Executing tests can be done via:

```bash
molecule test
```

This will create the test environment, i.e. the Docker container, run the linter, 
the `converge.yml` playbook and the tests (i.e. `verify.yml`).

After the execution the test environment is destroyed. If you want to preserve it
for further inspection you can run

```bash
molecule test --destroy=never
```

> Inspecting the container can be done with `molecule login`


### Executing manually

The series of actions to create the environment under which test will be executed
can be summarized as:

```bash
# create the docker container where tests will be run
molecule create

# enter the container to inspect the environment
molecule login

# run the converge.yml playbook that reference the role
molecule converge 

# test the role
molecule test --destroy=never

# destroy the container
molecule destroy
```

> [Testing Ansible Roles Using Molecule](https://medium.com/swlh/testing-ansible-roles-using-molecule-1dd00765cef6) by Martin Chrobot is a great article to start using Molecule


## License

The Ansible K-Box role is licensed under [MIT](./LICENSE).
