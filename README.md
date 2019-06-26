# K-Box
The K-Box is able to set up containerized K-Boxes on the host system. It
supports K-Box version 0.20.0 to 0.28.x.

This role also provides a way to upgrade to newer versions.
Downgrading back to older versions is not supported.

## Requirements

A working docker and reverseproxy installation.

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
      k_box: "klinktech/k-box:0.28.0"
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
