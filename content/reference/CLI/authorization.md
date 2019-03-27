---
title: Authorization with pxctl
linkTitle: Authorization
keywords: portworx, container, Kubernetes, storage, auth, authz, authorization, authentication, login, token, context, generate
description: Learn to enable auth in your px cluster
weight: 3
---

## Overview 

This document outlines how to interact with an auth-enabled PX cluster. 

## Context

pxctl allows you to store contexts and associated clusters, privileges, and tokens locally in your `~/.pxctl/contextconfig.yaml`

This enables you to easily switch between these configurations with a few commands:

```text
/opt/pwx/bin/pxctl context --help
```
```
Portworx pxctl context commands for setting authentication and connection info

Usage:
  pxctl context [flags]
  pxctl context [command]

Available Commands:
  create      create a context
  delete      delete a context
  list        list all contexts
  set         set the current context
  unset       unset the current context

```

### Context management
You can easily create and delete contexts with the below commands:

__Creating or updating a context:__
```text
/opt/pwx/bin/pxctl context create <context> --token <token> --endpoint <endpoint>
```
    
__Deleting a context:__
```text
/opt/pwx/bin/pxctl context delete <context>
```

__Listing your contexts:__

Your context is store in `~/.pxctl/contextconfig`, but you can easily view it with the `list` subcommand:

```text
/opt/pwx/bin/pxctl context list
```
```
contextconfig:
  current: user
  configurations:
  - context: user
    token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImpzdGV2ZW5zQHBvcnR3b3J4LmNvbSIsImV4cCI6MTU1MzcyNTMyMSwiZ3JvdXBzIjpbInB4LWVuZ2luZWVyaW5nIiwia3ViZXJuZXRlcy1jc2kiXSwiaWF0IjoxNTUzNjM4OTIxLCJpc3MiOiJwb3J0d29yeC5jb20iLCJuYW1lIjoiSmltIFN0ZXZlbnMiLCJyb2xlcyI6WyJzeXN0ZW0udXNlciJdLCJzdWIiOiJqc3RldmVuc0Bwb3J0d29yeC5jb20vanN0ZXZlbnMifQ.pZDbCIL7ldcImvIaNSjk18Ah3LqxX63MV378NiauRwk
    identity:
      subject: jstevens@portworx.com/jstevens
      name: Jim Stevens
      email: jstevens@portworx.com
    endpoint: http://localhost:9001
```


### Current context

Now that you've created your contexts, you can easily switch between them with the commands below.

__Setting current context:__

```text
/opt/pwx/bin/pxctl context set <context>
```

__Unsetting current context:__

```text
/opt/pwx/bin/pxctl context unset
```

## Generating tokens
PX supports two methods of authorization: OIDC and self-signed. 

* For generating a token through your OIDC provider, your provider's documentation on generating bearer tokens.
* For self-signed, pxctl has a command for generating token.

__Generating self-signed tokens:__ pxctl allows you to generate self-signed tokens in a few different ways: ECDSA, RSA, and Shared-Secret.

```text
pxctl auth token generate --auth-config=<authconfig.yaml> --issuer <issuer> \
    --ecdsa-private-keyfile <ecdsa key file> OR \
    --rsa-private-keyfile <rsa key file> OR \
    --shared-secret <secret>
```

__authconfig.yaml:__
```text
name: Jim Stevens
email: jstevens@portworx.com
sub: jstevens@portworx.com/jstevens
roles: ["system.user"]
groups: ["*"]
```
