<br>

- 01 Command For Show all user and current user
- 02 Drop a user
- 03 Create a new user AND Give privilege And Create table and give other user to show that table And a question
- 04 Change password
- 05 Create profile
- 06 Error Handaling while create a trigger
<br>

# `#01 show all the user`

<br>

```sql 
select username from dba_users
```

**Show the login user:**

```sql
show user;
```
<br>

--- 

<br>

# `#02 drop user`

<br>

```sql 
drop user username_name
```

### What Does `CASCADE` Do?

When you execute:
```sql
DROP USER C##YASIN CASCADE;
```
- **Drops the user**: Removes the `C##YASIN` user account from the database.
- **Drops all owned objects**: Deletes all database objects (e.g., tables, views, procedures) created by `C##YASIN`.
- **Cleans up dependencies**: Ensures no residual objects or references remain that could cause issues later.


<br>

--- 

<br>

# `03 # Create a new user`

<br>

`**Create a new user AND Give privilege And Create table and give other user to show that table**`

- `create user: C## if we use podman to run database.`

    ```sql 
        create user C##Yasin identified by 12345;
    ```

<br>

- `try to connect user C##Yasin`

    ```sql 
        connect C##Yasin/12345;

        ERROR:
        ORA-01045: Login denied. User C##YASIN does not have CREATE SESSION privilege.
        Help: https://docs.oracle.com/error-help/db/ora-01045/

    ```

<br>

- `give or grant privilege to the user user C##Yasin`

    Most uses privilege: **1. Create Session** অনুমতি দ্বারা ব্যবহারকারী ডেটাবেসে লগইন করতে পারে, **2. Create Table** অনুমতি দ্বারা নতুন টেবিল তৈরি করা যায়, **3. Create View** অনুমতি দ্বারা ভিউ তৈরি করা যায়, এবং **4. Create Synonym** অনুমতি দ্বারা সিননিম তৈরি করা যায় যা অন্য নাম দিয়ে টেবিল বা ভিউকে অ্যাক্সেস করতে সাহায্য করে।

    ```sql 
        grant create session,create table,create view,create synonym to C##Yasin;
    ```
<br>

- `Allocate memory to the user C##YASIN:`
    
    give unlimited space to user:
    ```sql
        alter user C##YASIN Quota unlimited on users;
    ```
    or  `for linux if we are using container podman or docker.`
    ```sql
        alter user C##YASIN Quota unlimited on system;
    ```

    give limited space to user(here 100MB)-> 100M:

    ```sql
        alter user C##Yasin quota 100M on users;
    ```

<br>

**1.** Create a user named Person2, give password 1234, give privilege CREATE SESSION to Person2; 
Create another user named Person1, give password 5678, give privilege CREATE SESSION, CREATE TABLE, CREATE VIEW, CREATE SYNONYM to Person1; create a table named NEWSPAPER under user Person1, insert data into NEWSPAPER table as user Person1, give privileges SELECT, insert ON NEWSPAPER table TO Person2 from user Person1; Show NEWSPAPER table and insert data into NEWSPAPER table from user Person2.

```sql
SQL> -- 1. Create a user named Person2, give password 1234, give privilege CREATE SESSION to Person2;
SQL> CREATE USER Person2 IDENTIFIED BY "1234";

User created.
SQL> GRANT CREATE SESSION TO Person2;

Grant succeeded.

SQL> CONNECT Person2/1234;
Connected.
SQL> -- Create another user named Person1, give password 5678,
SQL> CONNECT SYS/1234 AS SYSDBA;
Connected.
SQL> CREATE USER Person1 IDENTIFIED BY "5678";

User created.
SQL> -- Give privilege CREATE SESSION, CREATE TABLE, CREATE VIEW, CREATE SYNONYM to Person1; 
SQL> GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW,CREATE SYNONYM TO C##Bob;

Grant succeeded.

SQL> -- Create The table name NEWSPAPER Under Person1
SQL> CONNECT Person1/5678;
Connected.
SQL> -- create a table named NEWSPAPER
SQL> CONNECT SYS/1234 AS SYSDBA;
Connected.
-- tablespace system, for linux
SQL> ALTER USER Person1 DEFAULT TABLESPACE USERS;

User altered.

SQL> ALTER USER Person1 QUOTA 10M ON USERS;

User altered.

SQL> CONNECT Person1/5678;
Connected.
SQL> CREATE TABLE NEWSPAPER (
  ID NUMBER PRIMARY KEY,
  TITLE VARCHAR2(40),
  AUTHOR VARCHAR2(40)
  );

Table created.
SQL> --Show NEWSPAPER table and insert data into NEWSPAPER table from user Person2.
SQL> INSERT INTO NEWSPAPER (ID, TITLE, AUTHOR)
   VALUES (1, 'The is first newspaper','yasin arafat');

1 row created.

SQL> insert into NEWSPAPER (ID, TITLE, AUTHOR)
  2  VALUES(2, 'DATABASE WORLD', 'JANE SMITH');

1 row created.

SQL> GRANT SELECT, INSERT ON NEWSPAPER TO Person2;

Grant succeeded.

SQL> CONNECT Person2/1234;
Connected.
SQL> SELECT * FROM Person1.NEWSPAPER;

        ID TITLE
---------- ----------------------------------------
AUTHOR
----------------------------------------
         1 The Oracle Times
John Doe

         2 DATABASE WORLD
JANE SMITH


SQL> INSERT INTO Person1.NEWSPAPER (ID, TITLE,AUTHOR)
  2  VALUES (3, 'CSE FACULTY', 'CSE-19');

1 row created.
SQL> CONNECT Person2/1234;
Connected.
SQL> SELECT * FROM Person1.NEWSPAPER;

        ID TITLE
---------- ----------------------------------------
AUTHOR
----------------------------------------
         1 The Oracle Times
John Doe

         2 DATABASE WORLD
JANE SMITH

         3 CSE FACULTY
CSE-19


SQL>
```



# `#04 Change passwoard from system user to another user:`

<br>

```sql 
alter user C##yasin identified by 1234;
```

<br>

--- 

<br>

# `#05 Create profile:`

<br>

- FAILED_LOGIN_ATTEMPTS 3
    একজন ব্যবহারকারী টানা ৩ বার ভুল পাসওয়ার্ড দিলে তার অ্যাকাউন্ট লক হয়ে যাবে।

- PASSWORD_LOCK_TIME 1
    ভুল পাসওয়ার্ডের কারণে অ্যাকাউন্ট লক হলে, সেটি ১ দিন পর স্বয়ংক্রিয়ভাবে আনলক হবে।

- PASSWORD_LIFE_TIME 4
    পাসওয়ার্ডের মেয়াদ ৪ দিন, এর পর নতুন পাসওয়ার্ড সেট করতে হবে।

- PASSWORD_GRACE_TIME 2
    পাসওয়ার্ডের মেয়াদ শেষ হওয়ার পর ২ দিন সময় দেওয়া হবে নতুন পাসওয়ার্ড সেট করার জন্য।

- PASSWORD_REUSE_MAX 3
    একজন ব্যবহারকারী আগের ৩টি পাসওয়ার্ড পুনরায় ব্যবহার করতে পারবে না।

- PASSWORD_REUSE_TIME 4
    ব্যবহারকারী ৪ দিন পর আগের পাসওয়ার্ড পুনরায় ব্যবহার করতে পারবে।

যদি,**PASSWORD_REUSE_TIME unlimited** সেট করা হয়?
এর অর্থ হবে যে, ব্যবহারকারীরা তাদের পুরোনো পাসওয়ার্ড যতবার খুশি পুনরায় ব্যবহার করতে পারবে।
এটি নিরাপত্তার জন্য ঝুঁকিপূর্ণ, কারণ কেউ একই পাসওয়ার্ড বারবার ব্যবহার করলে পাসওয়ার্ড চুরি বা ব্রুট ফোর্স আক্রমণের সম্ভাবনা বেড়ে যায়।

```sql 
    CREATE PROFILE C##custom_profile LIMIT 
    FAILED_LOGIN_ATTEMPTS 3 
    PASSWORD_LOCK_TIME 1 
    PASSWORD_LIFE_TIME 4 
    PASSWORD_GRACE_TIME 2 
    PASSWORD_REUSE_MAX 3 
    PASSWORD_REUSE_TIME 4;
```

**CREATE USER WITH EXISTING PROFILE:**

```sql 
    create user C##YASIN identified by 1234 profile C##custom_profile;
```
<br>

**SEE ALL THE PROFILE AND DROP PROFILE:**

```sql 
select profile from dba_profiles;
drop profile profile_name;
```
<br>

**Unlocked a locked user account**
```sql 
ALTER USER C##JANE ACCOUNT LOCK;
```

<br>

**If the password is expire then set a new password**
```sql
ALTER USER C##jane PASSWORD EXPIRE;
```
<br>

**update a profile with newprivillege:**
```sql
ALTER PROFILE C##LIMITED_PROFILE LIMIT
        PASSWORD_LIFE_TIME 10
        PASSWORD_GRACE_TIME 8
        PASSWORD_REUSE_MAX 3
        PASSWORD_LOCK_TIME 1
        FAILED_LOGIN_ATTEMPTS 2
        PASSWORD_REUSE_TIME UNLIMITED;
```
<br>

