# RDS/Aurora MariaDB Migration & Restore

## Overview

I personally performed an RDS/Aurora MariaDB migration and restore for a staging environment supporting a WordPress workload.

The recovery objective was to minimize data loss and keep application downtime as short as possible.

The main concern was losing recent transactional data or allowing WordPress to operate against an inconsistent database state.

## Environment

* AWS RDS / Aurora MariaDB
* WordPress
* Database backups
* Restore point
* Application environment

## Recovery Objectives

The migration focused on:

* Minimizing potential data loss
* Keeping application downtime as short as practical
* Preserving database consistency
* Maintaining a clear rollback path

## Investigation and Preparation

I first validated:

* Source database state
* RDS configuration
* Available backups
* Restore point
* Database connectivity
* Application dependencies

The objective was to make sure the source environment and recovery point were suitable before performing the migration.

## Migration / Restore

I performed the database migration/restore and then validated the new RDS environment.

Validation included:

* Database connectivity
* Schema
* Data integrity
* Replication/failover behavior
* Application connectivity

The database was not considered ready simply because the RDS service was available.

## Rollback Strategy

I maintained the original database environment until the new RDS environment had successfully passed the required database and application validation.

This provided a rollback path if the restored environment failed validation.

The rollback decision was therefore based on application behavior and database validation rather than infrastructure availability alone.

## Application Validation

The final validation step was performed from the WordPress application.

I confirmed that WordPress could successfully operate against the new database environment.

The migration was considered successful only after validating the application behavior, rather than stopping after confirming that the database itself was available.

## Engineering Approach

The migration followed a controlled sequence:

```text
Source Database
      |
      v
Validate Source
      |
      v
Validate Backup / Restore Point
      |
      v
Migration / Restore
      |
      v
Database Validation
      |
      +----> Connectivity
      |
      +----> Schema
      |
      +----> Data Integrity
      |
      +----> Replication / F
```
