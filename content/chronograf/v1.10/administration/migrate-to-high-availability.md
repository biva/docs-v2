---
title: Migrate to a Chronograf HA configuration
description: >
  Migrate a Chronograf single instance configuration using BoltDB to a Chronograf high-availability (HA) cluster configuration using etcd.
menu:
  chronograf_1_10:
    weight: 10
    parent: Administration
---

Use [`chronoctl`](/chronograf/v1.10/tools/chronoctl/) to migrate your Chronograf configuration store from BoltDB to a shared `etcd` data store used for Chronograf high-availability (HA) clusters.

{{% note %}}
#### Update resource IDs
Migrating Chronograf to a shared data source creates new source IDs for each resource.
Update external links to Chronograf dashboards to reflect new source IDs.
{{% /note %}}

1. Stop the Chronograf server by killing the `chronograf` process.
2. To prevent data loss, we **strongly recommend** that you back up your Chronograf data store before migrating to a Chronograf cluster.
3. [Install and start etcd](/chronograf/v1.10/administration/create-high-availability/#install-and-start-etcd).
4. Run the following command, specifying the local BoltDB file and the `etcd` endpoint beginning with `etcd://`.
   (We recommend adding the prefix `bolt://` to an absolute path.
   Do not use the prefix to specify a relative path to the BoltDB file.)

    ```sh
    chronoctl migrate \
      --from bolt:///path/to/chronograf-v1.db \
      --to etcd://localhost:2379
    ```

    ##### Provide etcd authentication credentials
    If authentication is enabled on `etcd`, use the standard URI basic
    authentication format to define a username and password. For example:

    ```sh
    etcd://username:password@localhost:2379
    ```

    ##### Provide etcd TLS credentials
    If TLS is enabled on `etcd`, provide your TLS certificate credentials using
    the following query parameters in your etcd URL:

    - **cert**: Path to client certificate file or PEM file
    - **key**: Path to client key file
    - **ca**: Path to trusted CA certificates

    ```sh
    etcd://127.0.0.1:2379?cert=/tmp/client.crt&key=/tst/client.key&ca=/tst/ca.crt
    ```

5. Update links to Chronograf (for example, from external sources) to reflect your new URLs:
    - **from BoltDB:**
    http://localhost:8888/sources/1/status
    - **to etcd:**
    http://localhost:8888/sources/373921399246786560/status
6. Set up a load balancer for Chronograf.
7. [Start Chronograf](/chronograf/v1.10/administration/create-high-availability/#start-chronograf).
