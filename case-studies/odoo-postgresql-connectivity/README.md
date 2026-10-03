# Odoo / PostgreSQL Production Connectivity Incident

## Overview

I investigated and remediated a PostgreSQL connectivity incident affecting an Odoo production environment.

The objective was to restore stable application-to-database connectivity and keep Odoo background jobs operating normally.

## Environment

* Odoo
* PostgreSQL
* psycopg2
* Linux
* Network firewall
* `pg_hba.conf`
* Cloudflare-protected web services

## Impact

Odoo reported unexpected PostgreSQL connection termination.

The failure propagated into an Odoo cron worker, creating a risk that scheduled background operations could fail or remain incomplete.

## Initial Hypotheses

I considered:

1. PostgreSQL service failure
2. Database resource or checkpoint problems
3. Application connection issues
4. Authentication failures
5. Unexpected external traffic reaching PostgreSQL

## Investigation

I correlated Odoo application logs with PostgreSQL server logs.

Odoo reported a `psycopg2.OperationalError` indicating that the PostgreSQL connection had unexpectedly closed, followed by a `RuntimeError` in the Odoo cron thread.

I then reviewed:

* PostgreSQL activity
* Authentication failures
* Connection behavior
* Checkpoint activity
* Database roles
* Network access patterns

## Evidence

PostgreSQL remained operational and continued completing checkpoints.

At the same time, PostgreSQL logs showed:

* Connection attempts using non-existent roles
* Malformed PostgreSQL protocol requests
* Authentication attempts against the `postgres` account

The observed traffic was consistent with automated external scanning against an exposed PostgreSQL service rather than normal Odoo application traffic.

## Root Cause Analysis

The evidence did not indicate a PostgreSQL service failure.

The database was operational, but the database access path allowed unsolicited connection attempts that were contributing to the observed connection noise and required hardening.

## Remediation

I restricted PostgreSQL access using:

* `pg_hba.conf`
* Network firewall controls

Only the Odoo application and explicitly authorized administration sources were allowed to connect.

I also reviewed Cloudflare protections for the web-facing services while keeping the PostgreSQL access path separate from the HTTP/WAF layer.

## Validation

After remediation I validated:

* PostgreSQL availability
* Application authentication
* Odoo database connectivity
* Odoo cron execution
* Database access behavior
* Previously observed unauthorized connection patterns

The incident was considered resolved after Odoo maintained normal database connectivity and scheduled jobs executed successfully.

## Key Engineering Lesson

Database incidents should be investigated across both the application and database layers.

A database connection error does not automatically mean that the database service itself is unavailable.

Correlating application errors, database health, authentication activity, and network behavior allowed the failure domain to be narrowed before applying the remediation.

## Skills Demonstrated

* PostgreSQL
* Odoo
* Linux
* Network security
* `pg_hba.conf`
* Firewall hardening
* Incident response
* Log correlation
* Root-cause analysis
* Production troubleshooting
