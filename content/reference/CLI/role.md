---
title: Role with pxctl
linkTitle: Role
keywords: portworx, container, Kubernetes, storage, role, roles, authorization, authentication, login, token, context, generate
description: Learn to enable auth in your px cluster
weight: 3
---

## Overview 

This document outlines how to manage your own custom roles for fine-grained access control within your PX clusters.

## Default Roles
PX comes with a few standard roles that you can use when issuing tokens to users:

* __system.admin:__ can run any command
* __system.view:__ can only run read-only commands
* __system.user:__ can only access volume lifecycle commands

## Custom Roles
If you want more fine-grained control over what users can do within your clusters, you can manage your own custom roles with the following commands:

* `pxctl role create --role-config <path to JSON config>`
* `pxctl role delete --name <role name>`
* `pxctl role list`
* `pxctl role update --role-config <path to JSON config>`


### Creating a custom role
You can create a custom role by creating a JSON configuration for that role as using the `pxctl role create` command:
```
pxctl role create --help
Create a role using a json file which specifies the role and its rules.  A role consist of a set of rules defining services
and api's which are allowable.
e.g. Rule file which allows inspection of any object and listings of only volumes:
  {
     "name": "test.view",
     "rules": [
       {
         "services": [
           "volumes"
         ],
         "apis": [
           "*enumerate*"
         ]
       },
       {
         "services": [
           "*"
         ],
         "apis": [
           "inspect*"
         ]
       }
     ]
  }

Usage:
  pxctl role create [flags]

Examples:
pxctl role create --role-config <path to json file>

Flags:
      --role-config string   (Required) create role using role json file
  -h, --help                 help for create
```

### Role configuration
A role configuration is comprised of a name and a list of rules. Each rule has the following:

* __Services:__ Which services you want to provide access to. 
* __APIs:__ Which APIs you want to provide access to. You can use a simple regular expression to represent multiple APIs. i.e. to allow all enumerate APIs, add an entry `*enumerate*` to your `"apis"` array (see below).

__Example configuration (role.json):__

The following example configuration only allows access to volume enumerate commands and aall inspect APIs for every service.
```text
 {
     "name": "test.view",
     "rules": [
       {
         "services": [
           "volumes"
         ],
         "apis": [
           "*enumerate*"
         ]
       },
       {
         "services": [
           "*"
         ],
         "apis": [
           "inspect*"
         ]
       }
     ]
  }
```
### Services and APIs

To see all services and APIs you can use within your custom roles, see our [API documentation](https://libopenstorage.github.io/w/reference.html).

### Other role commands
* For __deleting__ a role, you can use `pxctl role delete <name>`
* To __list__ all roles, you can use `pxctl role list`
* To __update__ a role, you can use `pxctl role update --role-config <path to JSON config>`. This role-config is the same used in `pxctl role create`.

## Using your custom roles
Once you've created your custom roles, you can simply add the role names during token generation/user management.

* For OIDC, see your provider documentation on how to add the `roles` identifier to your tokens. __Note:__ Some OIDC providers have differently scoped roles at the system or user level. Please ensure that you've added the roles at the base level of the token.
* For self-signed tokens, add the custom role in your auth-config during token creation:

```text
name: Jim Stevens
email: jstevens@portworx.com
sub: jstevens@portworx.com/jstevens
roles: ["test.view"]
groups: ["*"]
```

This token will now allow access to whichever services and APIs were defined in the custom role `test.view`.

