<br>

- 01 Command For Show all user
- 02 Drop a user
- 03 Create a new user AND Give privilege
- 04 Change password
- 05 Create profile

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
<br>

--- 

<br>

# `03 # Create a new user`

<br>

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

--- 

<br>

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

**SEE ALL THE PROFILE AND DROP PROFILE:**

```sql 
select profile from dba_profiles;
drop profile profile_name;
```

