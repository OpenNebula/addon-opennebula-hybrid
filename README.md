# OpenNebula Hybrid Driver

## Description

Cloud bursting is a model in which the local resources of a Private Cloud are combined with resources from remote Cloud providers. This driver offers the possibility to implement Cloud bursting by deploying Virtual Machines seamlessly on a remote OpenNebula installation.

## Authors

* [OpenNebula Core Team](https://github.com/OpenNebula)

## Features

* Cloud Bursting to a remote OpenNebula.
* Manage remote instances importing them as 'wild VMs'.

## Compatibility

This add-on is compatible with:

| OpenNebula Version | Compatibility                                    |
| ------------------ | ------------------------------------------------ |
| 5.0                | Works from the CLI and advanced Sunstone dialogs |
| 5.2                | Fully integrated in Sunstone                     |

## Installation

To install this add-on, clone it and run install.sh:

    sudo ./install.sh -u oneadmin -g oneadmin

You will need to add the following to `/etc/one/oned.conf`: [oned.conf for hybrid OpenNebula](oned-extra.conf)

## Configuration

There are two configuration files:

* `/etc/one/opennebula_driver.conf`: Configures access to the remote OpenNebula: username, password, endpoint
* `/etc/one/opennebula_driver.default`: Default values for templates deployed in a remote OpenNebula.

## Usage

#### Configure the remote OpenNebula

Ask the remote OpenNebula administrator to create a new user for you. This user should have access to VM Templates ready to be instantiated.

#### Configure the local OpenNebula

Edit `/etc/one/opennebula_driver.conf` to add a new region for the remote OpenNebula. You may also use the default section.

```yaml
regions:
    remotehostname:
        user: remoteuser
        password: abc
        endpoint: http://remotehostname:2633/RPC2
        # Capacity is taken from the user and group quotas of the remote
        # OpenNebula user. Alternatively, you can set a hard limit here
        capacity:
            # In percentage. 800 means 8 cpu cores. 0 to use remote quotas
            cpu: 0
            # In MB. 0 to use remote quotas
            memory: 0
```

Then create a new Host with `im` and `vm` drivers set to `opennebula`.

#### Create a local hybrid VM Template

Your hybrid VM Template must contain this section. Set TEMPLATE_ID to the target VM Template ID in the **remote OpenNebula**.

```
PUBLIC_CLOUD=[
  TEMPLATE_ID="123",
  TYPE="opennebula" ]
```

If this Template does not define a local disk and must be deployed only in the remote OpenNebula instance, add this requirement:

```
SCHED_REQUIREMENTS = "PUBLIC_CLOUD = YES"
```

To match the reported allocated Host resources with the actual usage in the remote OpenNebula, set the same CPU and MEMORY as the remote Template.

## Development

To contribute bug patches or new features, you can use the github Pull Request model. It is assumed that code and documentation are contributed under the Apache License 2.0.