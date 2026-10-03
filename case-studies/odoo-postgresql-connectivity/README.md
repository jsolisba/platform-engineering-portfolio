Incident Response + database operations + security hardening 

History 

Odoo
  ↓
psycopg2
  ↓
PostgreSQL
  ↓
Network / firewall
  ↓
External traffic

Process
Odoo errors
      ↓
PostgreSQL logs
      ↓
Unexpected connection attempts
      ↓
Differentiate application traffic
from external scanning
      ↓
Harden DB access path
      ↓
Validate Odoo + cron + PostgreSQL
