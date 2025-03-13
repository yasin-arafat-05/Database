<br>

# `Oracle Download with Podman in arch linux: `

<br>


[downlaod_link_blog](https://sharafat.is-a.dev/notes/oracle-db/)

**01. Run in terminals:**
```bash
    podman exec -it oracle-yasin sqlplus system/12345@localhost:1521/free
```

<br>
<br>

# `# Connection with VsCode: `

### 1. Download the extension below: 

[extention_Oracle_Vscode](https://marketplace.visualstudio.com/items?itemName=Oracle.sql-developer)

![image](img/image01.png)

<br>
<br>

---
---

<br>

---
---

<br>

# `Basic Command:`

<br>

**1. Change linesize and pagesize in terminal:**

```bash
SET LINESIZE 200;
SET PAGESIZE 50;
```

**2. Show all the created table:**
```bash 
select table_name from user_tables;
```
**3. see the schema of a table:**
```bash
DESCRIBE table_name;
```

<br>

# `1. **Oracle-এ ডাটাবেস এবং টেবিল তৈরি করার প্রক্রিয়া:**`  
podman exec -it oracle-yasin sqlplus system/12345@localhost:1521/free

Oracle-এ সরাসরি একটি ডাটাবেস তৈরি করা যায় না যেভাবে MySQL-এ করা যায়। Oracle-এ "ডাটাবেস" বলতে একটি সম্পূর্ণ **Instance** বোঝায়। তবে, আপনি একটি **User Schema** তৈরি করতে পারেন এবং তাতে টেবিল তৈরি করতে পারেন।  

---

### **১. একটি নতুন ইউজার (Schema) তৈরি করুন:**
```sql
CREATE USER my_database IDENTIFIED BY my_password;
```

### **২. ইউজারকে Privileges দিন:**
```sql
GRANT CONNECT, RESOURCE TO my_database;
```

### **৩. নতুন ইউজারে লগ ইন করুন:**
```sql
CONNECT my_database/my_password;
```

---

### **৪. টেবিল তৈরি করুন:**
```sql
CREATE TABLE my_table (
    id NUMBER(5),
    name VARCHAR2(50)
);
```

এখন আপনি এই Schema-তে যত ইচ্ছা টেবিল তৈরি করতে পারেন। 


**To see all Oracle database instances created on a computer, you can use the following methods:**

```sql
SELECT instance_name FROM v$instance;
```

---
---

<br>
<br>

# `# Error Handingling trigger:`

<br>
<br>

```sql
SQL> update bookself set rating=3 where book_id=1;
update bookself set rating=3 where book_id=1
       *
ERROR at line 1:
ORA-04098: trigger 'SYSTEM.UPDATE_BOOK_RATING' is invalid and failed
re-validation
Help: https://docs.oracle.com/error-help/db/ora-04098/


SQL> alter trigger update_book_rating disable;

Trigger altered.

SQL> update bookself set rating=3 where book_id=4;

1 row updated.
SQL> select * from bookself_audit;

   BOOK_ID OLD_RATING NEW_RATING CHANGE_DA
---------- ---------- ---------- ---------
         4          5          3 09-MAR-25

SQL>
```



