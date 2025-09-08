
---

# 🐘 PostgreSQL on Arch Linux – Complete Cheatsheet

---



## ✅ Summary of the nodebook:

- 1. Install & start PostgreSQL
- 2. Understand **default superuser (`postgres`)**
- 3. Create roles & users with least privilege
- 4. Manage DB, schema, tables
- 5. Use `GRANT` / `REVOKE` for security
- 6. Monitor sessions & locks
- 7. Automate with functions & triggers
- 8. Backup & restore safely



<br>

# `#01. Installation & Service:`

<br>

```bash
# Install PostgreSQL
sudo pacman -S postgresql

# Initialize database cluster
sudo -iu postgres
initdb -D /var/lib/postgres/data

# Start service
sudo systemctl enable postgresql
sudo systemctl start postgresql
systemctl status postgresql
```

<br>

# `#02. Default User & Roles:`

<br>

* **System user:** `postgres`
* **Default DB superuser:** `postgres`
* Has full privileges (`SUPERUSER`, `CREATEDB`, `CREATEROLE`, `REPLICATION`)
* Do not use `postgres` directly in apps. Create dedicated users.

Check current session:

```sql
SELECT current_user;     -- current role in use
SELECT session_user;     -- login role
```

List all roles:

```sql
\du
```

<br>

# `#03. Create & Manage Roles:`

<br>

```sql
-- Create basic user
CREATE USER myuser WITH PASSWORD 'mypassword';

-- Create role with login
CREATE ROLE readonly LOGIN PASSWORD 'readonlypass';

-- Create user with privileges
CREATE USER dbadmin WITH PASSWORD 'securepass' CREATEDB;

-- Superuser (full access, avoid in production)
CREATE USER superyasin WITH PASSWORD 'superpass' SUPERUSER;

-- Alter user
ALTER USER myuser WITH CREATEDB;
ALTER USER myuser WITH PASSWORD 'newpass';

-- Drop user
DROP USER myuser;
```


<br>

# `#04. Database Management:`

<br>


```sql
-- Create database
CREATE DATABASE mydb OWNER myuser;

-- List databases
\l

-- Connect to database
\c mydb

-- Drop database
DROP DATABASE mydb;
```

<br>

# `#05. Schema Management:`

<br>


```sql
-- Create schema
CREATE SCHEMA app_schema AUTHORIZATION myuser;

-- List schemas
\dn

-- Drop schema
DROP SCHEMA app_schema CASCADE;
```

<br>

# `#06. Table Management:`

<br>


```sql
-- Create table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT now()
);

-- List tables
\dt

-- Describe table
\d users

-- Drop table
DROP TABLE users CASCADE;
```


<br>

# `#07. User Privileges:`

<br>


Types of privileges:

* `SELECT` → read data
* `INSERT` → insert new rows
* `UPDATE` → modify rows
* `DELETE` → delete rows
* `TRUNCATE` → empty table
* `REFERENCES` → use in foreign keys
* `TRIGGER` → create triggers
* `EXECUTE` → run functions/procedures
* `USAGE` → use schema
* `CONNECT` → connect to DB
* `CREATE` → create objects (tables, views, etc.)

Granting privileges:

```sql
-- Database level
GRANT CONNECT ON DATABASE mydb TO myuser;

-- Schema level
GRANT USAGE ON SCHEMA public TO myuser;
GRANT CREATE ON SCHEMA public TO myuser;

-- Table level
GRANT SELECT, INSERT, UPDATE, DELETE ON users TO myuser;

-- Grant all privileges on all tables
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO myuser;

-- Sequences (for SERIAL columns)
GRANT USAGE, SELECT, UPDATE ON ALL SEQUENCES IN SCHEMA public TO myuser;

-- Functions
GRANT EXECUTE ON FUNCTION user_count() TO myuser;
```

Revoking privileges:

```sql
REVOKE INSERT, UPDATE ON users FROM myuser;
```

Default privileges (for future objects):

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT ON TABLES TO readonly;
```

<br>

# `#08. Security Best Practices:`

<br>

```sql
-- Disable superuser for app users
CREATE USER appuser WITH PASSWORD 'secure' NOSUPERUSER NOCREATEDB NOCREATEROLE;

-- Force password expiration
ALTER USER appuser VALID UNTIL '2025-12-31';

-- Lock a role
ALTER ROLE baduser NOLOGIN;

-- Role membership (groups)
CREATE ROLE devs;
GRANT devs TO alice;
```

<br>

# `#09. Transactions:`

<br>


```sql
BEGIN;
UPDATE users SET name='Alice' WHERE id=1;
SAVEPOINT sp1;
DELETE FROM users WHERE id=2;
ROLLBACK TO sp1;  -- undo delete
COMMIT;
```

<br>

# `#10. Functions & Stored Procedures:`

<br>


```sql
-- Simple function
CREATE FUNCTION get_user_count()
RETURNS integer AS $$
    SELECT COUNT(*) FROM users;
$$ LANGUAGE SQL;

-- PL/pgSQL function
CREATE OR REPLACE FUNCTION add_user(uname TEXT, uemail TEXT)
RETURNS void AS $$
BEGIN
    INSERT INTO users (name, email) VALUES (uname, uemail);
END;
$$ LANGUAGE plpgsql;
```


<br>

# `#10. Trigger:`

<br>


```sql
-- Audit table
CREATE TABLE user_audit (
    id SERIAL PRIMARY KEY,
    user_id INT,
    action TEXT,
    created_at TIMESTAMP DEFAULT now()
);

-- Trigger function
CREATE OR REPLACE FUNCTION log_user_changes()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO user_audit (user_id, action)
    VALUES (NEW.id, TG_OP);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Attach trigger
CREATE TRIGGER user_change_trigger
AFTER INSERT OR UPDATE OR DELETE ON users
FOR EACH ROW
EXECUTE FUNCTION log_user_changes();
```


<br>

# `#12. Monitoring & Diagnostics:`

<br>


```sql
-- List current connections
SELECT * FROM pg_stat_activity;

-- Show locks
SELECT * FROM pg_locks;

-- Show database size
SELECT pg_size_pretty(pg_database_size('mydb'));

-- Show table size
SELECT pg_size_pretty(pg_total_relation_size('users'));
```

<br>

# `#13. Backup & Restore:`

<br>


```bash
# Backup database
pg_dump -U myuser mydb > backup.sql

# Restore database
psql -U myuser -d mydb -f backup.sql

# Full cluster backup
pg_dumpall -U postgres > full_backup.sql
```

<br>

# `#14. Connect with GUI Tools:`

<br>


* **pgAdmin** → `sudo pacman -S pgadmin4`
* **DBeaver / DataGrip** → Connect with:

  * Host: `localhost`
  * Port: `5432`
  * User: `myuser`
  * Database: `mydb`




