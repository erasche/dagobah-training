layout: true
class: inverse, top, large

---
class: special
# Using Heterogeneous compute resources

slides by @natefoo

.footnote[\#usegalaxy / @galaxyproject]

---
# What are heterogenous compute resources?

Differences in:
- Operating system or version
- Users/groups
- Data accessibility
- Administrative control

Galaxy expects:
- One OS, version (dependencies)
- Shared filesystem w/ fixed paths

---
# Partial solution - CLI job runner

SSH to remote, submit jobs with CLI `sbatch`, `qsub`, etc.

Still depends on shared FS

---
# Pulsar

Galaxy's remote job management system

Can run jobs on any(?) OS including Windows

Multiple modes of operation for every environment

---
# Pulsar - Architecture

Pulsar server runs on remote resource (e.g. cluster head node)

Galaxy Pulsar job runner is Pulsar client

Transport is HTTP or AMQP, language is JSON

---
# Pulsar Transports - RESTful

Pulsar server listens over HTTP(S)

Pulsar client initiates connections to server

Good for:
- Environments where firewall, open ports are not concerns
- No external dependencies (AMQP server)

---
# Pulsar Transports - AMQP

Pulsar server and client connect to AMQP server

Good for:
- Firewalled/NATted remote compute
- Networks w/ bad connectivity

---
# Pulsar Transports - Embedded

Galaxy runs Pulsar server internally

Good for:
- Manipulating paths
- Copying input datasets from non-shared filesystem

---
# Pulsar - Job file staging

Pulsar can be configured to *push* or *pull* when using RESTful:
- Push
  - Galaxy sends job inputs, metadata to Pulsar over HTTP
  - Upon completion signal from Pulsar, Galaxy pulls from Pulsar over HTTP
- Pull
  - Upon setup signal, Pulsar pulls job inputs, metadata from Galaxy over HTTP
  - Upon completion, Pulsar pushes to Galaxy over HTTP

Pulsar can use libcurl for more robust transfers with resume capability

AMQP is pull-only because Pulsar does not run HTTP server

---
# Pulsar - Dependency management

Pulsar does not provide Tool Shed tool dependency management. But:
- It has its own dependency resolver config
- It can auto-install conda dependencies
- In many cases you can copy TS dependencies and adjust `env.sh` with `sed`
  - If the Galaxy server OS <= remote OS

---
# Pulsar - Job management

Pulsar "managers" provide job running interfaces:
- `queued_python`: Run locally on the Pulsar server
- `queued_drmaa`: Run on a cluster with DRMAA
- `queued_cli`: Run on a cluster with local `qsub`, `sbatch`, etc.
- `queued_condor`: Run on HTCondor

---
# Exercise

[Running jobs on remote resources using Pulsar](https://github.com/galaxyproject/dagobah-training/blob/2018-oslo/sessions/17-heterogenous/ex1-pulsar.md)
