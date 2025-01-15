<br>

# `#01 My sql with xmapp:`

<br>

Start the xmapp server and start Mysql database and Apache Web Server.
- we can change the port by clicking configure option.

# [installation_blog](https://sharafat.is-a.dev/notes/mysql/)

<br>



<br>

# `#02 Edge class: university database:`

<br>

intrigity constant,

- ON DELETE CASCADE vs ON DELETE SET NULL,

    [ON_DELETE_CASCADE_VS_ON_DLETE_SET_NULL_Blog_LINK](https://www.geeksforgeeks.org/difference-between-on-delete-cascade-and-on-delete-set-null-in-dbms/)

<br>
<br>


##
### প্রাইমারি মেমোরি (Primary Memory):  
1. **সংজ্ঞা:** প্রাইমারি মেমোরি হল কম্পিউটারের ভেতরের মেমোরি যা ডেটা এবং প্রোগ্রামগুলিকে অস্থায়ীভাবে সংরক্ষণ করে।  
2. **গতি:** এটি খুব দ্রুত কাজ করে।  
3. **ধারণক্ষমতা:** প্রাইমারি মেমোরির স্টোরেজ সীমিত হয় (যেমন, RAM 4GB, 8GB ইত্যাদি)।  
4. **ধরন:** প্রধানত দুটি -  
   - RAM (Random Access Memory)  
   - ROM (Read-Only Memory)  
5. **ডেটা স্থায়িত্ব:** RAM-এর ডেটা কম্পিউটার বন্ধ হলে মুছে যায়।  
6. **মূল্য:** এটি তুলনামূলকভাবে দামি।  
7. **ব্যবহার:** ডেটা এবং প্রোগ্রামকে প্রসেস করার সময় তা দ্রুত অ্যাক্সেসের জন্য ব্যবহৃত হয়।  

### সেকেন্ডারি মেমোরি (Secondary Memory):  
1. **সংজ্ঞা:** সেকেন্ডারি মেমোরি হল কম্পিউটারের বাইরের মেমোরি যা ডেটা এবং ফাইলকে স্থায়ীভাবে সংরক্ষণ করে।  
2. **গতি:** এটি তুলনামূলকভাবে ধীর।  
3. **ধারণক্ষমতা:** সেকেন্ডারি মেমোরি সাধারণত বড় স্টোরেজ ধারণক্ষমতা রাখে (যেমন, হার্ড ড্রাইভ 1TB, 2TB ইত্যাদি)।  
4. **ধরন:** কিছু উদাহরণ -  
   - HDD (Hard Disk Drive)  
   - SSD (Solid State Drive)  
   - USB Drives  
   - Optical Disks (CD/DVD)  
5. **ডেটা স্থায়িত্ব:** সেকেন্ডারি মেমোরিতে ডেটা স্থায়ী থাকে, কম্পিউটার বন্ধ হলেও তা থাকে।  
6. **মূল্য:** এটি তুলনামূলকভাবে সস্তা।  
7. **ব্যবহার:** দীর্ঘমেয়াদি ডেটা সংরক্ষণ এবং ব্যাকআপের জন্য ব্যবহৃত হয়।  

**সংক্ষেপে:**  
- **প্রাইমারি মেমোরি** দ্রুত এবং অস্থায়ী, প্রসেসিংয়ের জন্য।  
- **সেকেন্ডারি মেমোরি** ধীর কিন্তু স্থায়ী, ডেটা সংরক্ষণের জন্য।  

<br>
<br>


## `Work With University database:`


**Database Schema:**

```sql
create table classroom
	(building		varchar(15),
	 room_number		varchar(7),
	 capacity		numeric(4,0),
	 primary key (building, room_number)
	);

create table department
	(dept_name		varchar(20), 
	 building		varchar(15), 
	 budget		    numeric(12,2) check (budget > 0),
	 primary key (dept_name)
	);

create table course
	(course_id		varchar(8), 
	 title			varchar(50), 
	 dept_name		varchar(20),
	 credits		numeric(2,0) check (credits > 0),
	 primary key (course_id),
	 foreign key (dept_name) references department (dept_name)
		on delete set null
	);

create table instructor
	(ID			varchar(5), 
	 name			varchar(20) not null, 
	 dept_name		varchar(20), 
	 salary			numeric(8,2) check (salary > 29000),
	 primary key (ID),
	 foreign key (dept_name) references department (dept_name)
		on delete set null
	);

create table section
	(course_id		varchar(8), 
         sec_id			varchar(8),
	 semester		varchar(6)
		check (semester in ('Fall', 'Winter', 'Spring', 'Summer')), 
	 year			numeric(4,0) check (year > 1701 and year < 2100), 
	 building		varchar(15),
	 room_number		varchar(7),
	 time_slot_id		varchar(4),
	 primary key (course_id, sec_id, semester, year),
	 foreign key (course_id) references course (course_id)
		on delete cascade,
	 foreign key (building, room_number) references classroom (building, room_number)
		on delete set null
	);

create table teaches
	(ID			varchar(5), 
	 course_id		varchar(8),
	 sec_id			varchar(8), 
	 semester		varchar(6),
	 year			numeric(4,0),
	 primary key (ID, course_id, sec_id, semester, year),
	 foreign key (course_id, sec_id, semester, year) references section (course_id, sec_id, semester, year)
		on delete cascade,
	 foreign key (ID) references instructor (ID)
		on delete cascade
	);

create table student
	(ID			varchar(5), 
	 name			varchar(20) not null, 
	 dept_name		varchar(20), 
	 tot_cred		numeric(3,0) check (tot_cred >= 0),
	 primary key (ID),
	 foreign key (dept_name) references department (dept_name)
		on delete set null
	);

create table takes
	(ID			varchar(5), 
	 course_id		varchar(8),
	 sec_id			varchar(8), 
	 semester		varchar(6),
	 year			numeric(4,0),
	 grade		        varchar(2),
	 primary key (ID, course_id, sec_id, semester, year),
	 foreign key (course_id, sec_id, semester, year) references section (course_id, sec_id, semester, year)
		on delete cascade,
	 foreign key (ID) references student (ID)
		on delete cascade
	);

create table advisor
	(s_ID			varchar(5),
	 i_ID			varchar(5),
	 primary key (s_ID),
	 foreign key (i_ID) references instructor (ID)
		on delete set null,
	 foreign key (s_ID) references student (ID)
		on delete cascade
	);

create table time_slot
	(time_slot_id		varchar(4),
	 day			varchar(1),
	 start_hr		numeric(2) check (start_hr >= 0 and start_hr < 24),
	 start_min		numeric(2) check (start_min >= 0 and start_min < 60),
	 end_hr			numeric(2) check (end_hr >= 0 and end_hr < 24),
	 end_min		numeric(2) check (end_min >= 0 and end_min < 60),
	 primary key (time_slot_id, day, start_hr, start_min)
	);

create table prereq
	(course_id		varchar(8), 
	 prereq_id		varchar(8),
	 primary key (course_id, prereq_id),
	 foreign key (course_id) references course (course_id)
		on delete cascade,
	 foreign key (prereq_id) references course (course_id)
	);
```

- char(5): `memory size always fix.`
- varchar(5) : `vary the memory size. "ab" -> it takes memory for two character from primary memory.`

### **`# NUMERIC(12,2)`**:
1. **সংজ্ঞা:**  
   - `NUMERIC(12,2)` এর অর্থ হলো:  
     - মোট **১২টি সংখ্যা** (ডিজিট) সংরক্ষণ করা যাবে।  
     - এর মধ্যে **২টি সংখ্যা দশমিক পয়েন্টের পরে থাকবে**।  
     - বাকিরা (১২ - ২ = ১০) দশমিক পয়েন্টের আগে থাকবে।  
   - উদাহরণ:  
     - সঠিক ইনপুট: `1234567890.12`, `0.99`, `99999.01`  
     - ভুল ইনপুট: `12345678901.12` (১২ ডিজিটের সীমা অতিক্রম করেছে), `12.123` (দশমিকের পরে ৩ সংখ্যা আছে)।  


### **`# CHECK (budget > 0)`**:
1. **সংজ্ঞা:**  
   - এই শর্তটি নিশ্চিত করে যে **`budget` কলামের মান ০ এর চেয়ে বড় হতে হবে**।  
   - অর্থাৎ, আপনি কোনো নেতিবাচক (negative) বা শূন্য (0) মান ইনসার্ট করতে পারবেন না।  
   - উদাহরণ:  
     - সঠিক ইনপুট: `1000.00`, `12.34`, `1.01`  
     - ভুল ইনপুট: `-500.00`, `0`, `-0.01`  


### **আপনার স্টেটমেন্টের কাজের নিয়ম**:
1. **নেতিবাচক মান (negative value):**  
   - `CHECK (budget > 0)` এর কারণে নেতিবাচক মান বা `0` ইনসার্ট করা যাবে না।  

2. **দশমিক পয়েন্টের সংখ্যা:**  
   - `NUMERIC(12,2)` এর কারণে দশমিকের পরে **শুধুমাত্র ২টি সংখ্যা** ইনসার্ট করা যাবে।  

3. **সীমা:**  
   - দশমিকসহ সর্বাধিক ১২টি সংখ্যা ইনসার্ট করা যাবে।  


### **`# ON DELETE SET NULL`:**  
- যখন প্যারেন্ট টেবিল থেকে একটি রেকর্ড মুছে ফেলা হয়, তখন চাইল্ড টেবিলের সংশ্লিষ্ট **ফরেন কি** মান **NULL** হয়ে যায়।  
- উদাহরণ:  
  প্যারেন্ট টেবিল থেকে `dept_id` মুছে ফেললে চাইল্ড টেবিলে `dept_id` **NULL** হবে।  


### **`# ON DELETE CASCADE`:**  
- যখন প্যারেন্ট টেবিল থেকে একটি রেকর্ড মুছে ফেলা হয়, তখন চাইল্ড টেবিলের সংশ্লিষ্ট **রেকর্ডও মুছে যায়**।  
- উদাহরণ:  
  প্যারেন্ট টেবিল থেকে `dept_id` মুছে ফেললে চাইল্ড টেবিলের সেই `dept_id` থাকা রেকর্ড **ডিলিট** হবে।  


**সংক্ষেপে:**  
- `SET NULL` -> ফরেন কি NULL হয়।  
- `CASCADE` -> চাইল্ড রেকর্ডও মুছে যায়।  


# `# Query on Table:`

- Show all the tables:
    ```sql
    show tables;
    ```
- Show all the tables with metadata:

```sql

```



