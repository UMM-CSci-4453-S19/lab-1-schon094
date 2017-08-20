# Lab 1

## Table of Contents

- [Lab 1](#lab-1)
  - [Broad overview](#broad-overview)
    - [Relational Databases](#relational-databases)
  - [MariaDB: a first pass](#mariadb-a-first-pass)
    - [Log into MariaDB](#log-into-mariadb)
    - [Poke around a bit](#poke-around-a-bit)
    - [Decide on a username](#decide-on-a-username)
  - [Watch a video about MySQL users](#watch-a-video-about-mysql-users)
  - [Create your user and database:](#create-your-user-and-database)
    - [Making a database](#making-a-database)
    - [Adjusting Privileges](#adjusting-privileges)
  - [Log in with your new account](#log-in-with-your-new-account)
    - [Setting your password](#setting-your-password)
  - [Exercises](#exercises)
  - [What to DO](#checklist-of-things-to-do)
  - [References](#references)

## Broad overview

A **database** may be defined as a structured set of data (more on what that might mean in later lessons).  

A **database management system (DBMS)** is the program(s) used to access and manipulate that data.  We will frequently abuse the term *database* and use it to refer to both the data **and** the program (which would, more properly, be called a DBMS)

Broadly speaking databases fall into two major categories:
  * SQL
  * NoSQL
  
SQL stands for **Structured Query Language**.  It is the language used to organize, add, remove, and modify the data in an SQL DBMS (big surprise right?).  It is also the language we will use to transform raw data into useable information.  A few well-known examples of SQL DBMS include MySQL, Oracle, SQL Server, and MariaDB.  In an SQL database information is stored as **tables**.  A table is a two-dimensional array of data.  The *rows* are known as **records**, and the columns are known as **fields**.

NoSQL is a broad term encompassing many types of databases that organize their data differently than SQL-style databases.  Some well known examples include mongoDB and Neo4J.  NoSQL databases give up consistency guarantees that an SQL DBMS can provide in exchange for improved scalability (which is why they are so popular for applications like Facebook).  There is much more variety in the way that a NoSQL database structures its data, but several are closely associated with structured data formats like XML or JSON.  

We are going to spend about two-thirds of our time with the SQL DBMS known as MariaDB.  MariaDB is the continuation of the MySQL project.  It split from MySQL after Oracle acquired Sun Microsystems in 2009 (and MySQL along with Sun).  Many of the original MySQL developers are MariaDB developers.  You can read more about the [history of MySQL and MariaDB](http://www.slideshare.net/Codemotion/my-sql-mariadbstorycodemotion).  (If you are interested then focus on the history-- there are many technical details that won't make sense without some DBF background)

We are going to focus on MariaDB because:
  * It is Open Source, 
  * actively developed, 
  * free,
  * powerful,
  * and increasingly includes NoSQL support.  

### Relational Databases

SQL databases are **relational Databases (RDBMS)**.  The data is organized around the idea of
* Databases
* Tables
* Records
* Fields

The data is an abstraction of the TABLE.  Most RDBMS use the SQL language to 
* extract data, 
* insert data, 
* update data and,
* create structures.

The entire organizational system is secretely based around the ideas of SETS or ordered elements which we will explore in greater detail later.

## MariaDB: a first pass

As you go through the exercises below, you may find the [references](#references) below useful.

### Log into MariaDB

1. Start by opening a terminal (ask if you need help with this step)
2. Run the following command:<br> `mysql -u tempadmin -p --host=<host> `<br>I will provide the password and hostname in class. (That's a security measure)

You are using the `mysql` client.  This is a typical command line interface. MOST commands are in a language called SQL and they need to end in a semicolon. Let's start simply:

###Poke around a bit

Look at the databases in the system:

```no-highlight
show databases;
```

(go ahead--you'll want to be typing this as you read).

On one metaphoric level, a database is like a folder in a file system, and you can change your default
database with the `USE` command. Mariadb does NOT allow nesting one database within another so
the metaphor breaks down pretty quickly. But it's still a handy way to think about it-- just pretend you can only go one-level deep. In a similar vein, the equivalent of `ls` (or `dir`) is `SHOW TABLES`.  You will get an error if you try to do this *without* selecting a database first (another way in which the metaphor fails).  Try the following:

Change to the database 'test':
```
use test;
```

See what tables are in that database:
```
show tables;
```
Look at the contents of database inventory (MariaDB IS case sensitive when it comes to table names-- be
careful):
```
select * from inventory;
```

Get a sense for the structure of that database:
```
describe inventory;
```

MariaDB (and mySQL) have an administrative database known as `mysql`.  It contains most of the important information necessary for the DBMS to work correctly so I'm taking a risk by allowing a remote login that has the ability to modify these tables… be careful with what you type please.

With that warning firmly in mind see if you can do the following:
* Change to the database `mysql`,
* look at the tables in that database, and
* examine the structure of the `user` table.

Now type the following:
```
select user, host, password from user;
```

The user column contains (surprise) the username.  The host column contains the scope for that username, and the password column contains the encrypted password.  

### Decide on a username

You will need to pick a username to access the DBMS.  Add your username to the table in [[MariaDB usernames|mariadb-usernames]].  You don't need to give me your password, but you'll want to keep it somewhere safe-- I can change it if you lose it.

You are going to use an administration accout to add yourself as a user and create a database for your in-class work.  You will need to be on campus in order to access or use MariaDB (either physically or via a VPN) for this purpose.  You will be able to provide access to your fellow students, or remove it.  Use your power wisely.

## Watch a video about MySQL users

Before doing anything else, watch the following video which explains a bit about how user accounts work on MySQL (and thus on MariaDB):

<http://theurbanpenguin.com/wp/index.php/understanding-mysql-users/>

Since most of your machines don't have audio... I'll play it on the projector (probably after showing you how to add a user)

## Create your user and database:

The video you watched introduces the commands `CREATE USER`, `DROP USER`, and `SET PASSWORD`. It also uses a
convention for accessing a table in a database without having to set that database as your default with
the `USE` command. It skips the `RENAME USER` command (which can be helpful).

Let's start by seeing how to create your user with the following template as a guide:
```
CREATE USER <name> IDENTIFIED BY '<password>';
```

For example, I might create the user `testy_pete` as follows:  `CREATE USER testy_pete IDENTIFIED BY 'let me in';`.  Do NOT add the word `PASSWORD` to the `CREATE USER` command-- that special word is used to indicate the presence of an encrypted password (this way you don't need to send an unencrypted password over an unsecure network channel).  The user `testy_pete` now exists.

Go ahead and give it a go with the name that you will use for your user.  Your new user has two drawbacks:  

1. It has very little in the way of privileges (barely enough to do *anything*)
2. There is no control over the hosts from which the user can connect.

Look at your user's privileges like this:

```
SHOW GRANTS FOR <name>;
```

I see something like this:

```
+-----------------------------------------------------------------------------------------------------------+
| Grants for testy_pete@%                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'testy_pete'@'%' IDENTIFIED BY PASSWORD '*C05F45ADBF3C3B19D8B4B676A97CAA34DF0F2A7F' |
+-----------------------------------------------------------------------------------------------------------+
```

Security in mySQL (and mariaDB) is managed through the use of scoped users and privileges.  Different privileges can be granted for users based upon their username AND the host from which they connect. The command I used to create `testy_pete`, could be written more explicitly as

```
CREATE USER testy_pete@'%' IDENTIFIED BY 'let me in';
```

In this construction the username is of the form `<name>@<host>`.  Wild cards can be used in both the `<name>` and the `<host>` side of things.  We are only going to discuss the use of '%' as a wild-card character in the host.

If you are running private ip in the `192.168.1.x` range you could use `192.168.1.%` for your host when you create your user.  Every machine in the dungeon has an IP address of the form `146.57.33.x`, so if I only wanted `testy_pete` to be able to use the database from the dungeon I could create the account thus:

```
CREATE USER testy_pete@'146.57.33.%' IDENTIFIED BY 'let me in';
```

(Please note  the use of single quotes-- they can be used on the host side too:

```
CREATE USER testy_pete@'146.57.33.%' IDENTIFIED BY 'let me in';
```

In just a second you are going to remove the user you just created, and make a new one that can only log in from the dungeon.  Before that take a second look at `mysql.user` while you are still logged in as tempadmin:

```
select user, host, password from mysql.user;
```

(note the use of the <database>.<table> construction).

Now remove your user from the system:

```
DROP USER <name>;
```

It is **very** common to use the keyword `DROP` to remove an object (a user, a database, a table, etc).

Now that you have removed your unrestricted account, re-add it with the appropriate HOST designator to restrict access to users in the dungeon (I'll be checking the host **and** the password as part of your grade).

Have a look at how the user database has changed:

```
select user, host, password from mysql.user;
```

### Making a database

You create a database with the `CREATE DATABASE <database name>` command and you delete it with
the `DROP DATABASE <database name>` command.

Create a database for your personal use.  Use the same name as your username.  My command would look something like this:

```
CREATE DATABASE testy_pete;
```

You will use this in future labs.

### Adjusting Privileges

MariaDB controls what you can do through the use of **privileges**.  Privileges can be **granted** or **revoked** (much as users can be **created** or **dropped**).

All of this information is stored in tables in the `mysql` database which means that you could
manipulate those tables directly… don't! Messing things up could be catastrophic. There are commands
for doing this sort of thing. You are mostly going to care about `GRANT` and `REVOKE`.

There are MANY levels of access that are allowed. Most are fairly straightforward. Here are a few:

* All: (everything except the ability to do grants of your own)
* ALTER: ability to change a table's structure
* CREATE: ability to create a table (but not add them)
* CREATE USER: ability to create, remove, rename users AND the ability to revoke all privileges from a user (but not add them)
* DROP: ability to remove a table
* SELECT: ability to pull information from a table
* INSERT: ability to add records to a table
* DELETE: ability to remove records from a table
* SHOW DATABASES: ability to use the command SHOW DATABASES and see ALL databases
* UPDATE: ability to update
* USAGE: counter-intuitively… no real rights to anything (I *THINK* it means they can still log-in)

Before doing anything else, have a look at what your new account can do.  Try the equivalent of the following with your new account:

```
SHOW GRANTS for testy_pete;
SHOW GRANTS for testy_pete@'146.57.33.%';
```

Your new username makes a few commands a bit more complicated.   See the difference?  You need to fully qualify your username.

To provide access use `GRANT`.  The basic usage is `GRANT <privs> ON <object> TO <user>`. The privileges can be applied to all databases (`*.*`), to all tables in a database (`<db>.*`), or to specific tables (`<db>.<table>`). 

The user can be scoped. So, for example

```
GRANT SELECT on test.A to 'dummy'@'192.%';
```

will give the user dummy the ability to extract information from table `A` in database `test` IF they are
logged in from an IP address starting with 192. (NOTE: IPV6 can be accommodated by this convention
too). WARNING: in mySQL the scoped user MUST have been previously created in order to grant the
privileges. In MariaDB the user will be automagically created if it doesn't already exist… this means that
the password for that scoped user might not be what you expect.

You are going to want to give yourself full access to your new database.  Modify the following command:

```
GRANT ALL ON testy_pete.* TO testy_pete@'146.57.33.%' WITH GRANT OPTION;
```

The keywords `WITH GRANT OPTION` will allow you the ability to provide privileges to your fellow classmates.

After you have given your new account appropriate access to your new database, you'll need to refresh the system:

```
FLUSH PRIVILEGES;
```

If you neglect this step you might not be able to use the new database from your new account.

## Log in with your new account

Give it a whirl.  Log out of the `tempadmin` account using `EXIT;`, and see if you can log in with your new account.

### Setting your password
You can use the following command to set your password once you are logged into MariaDB:

> `SET PASSWORD = PASSWORD('cleartext password');`

## Exercises

I'm going to ask you to do a series of tasks that will probably require you to look a few things up
online. I've included some references for this lab (down below), but in the future you'll want to figure out
how to google your way to the exact format of a command and examples. In particular you are going to need to figure out how the `CREATE TABLE` and `INSERT` statements work.  You will also need to figure out a little bit about SQL data-types.  If you have any questions feel free to ask (I plan on providing a minilecture on the subject).  With that in mind, here's what I
want you to do (or have already done):

1. Create your database (this should already be completed).  You will use this database for all your work.
1. Create a table called `test` with ONE column named `test_field`<BR>the data-type of `test_field` must be TEXT and you should insert your last name into the table.
1. Give the user `peter` full privileges to your database:<BR>`grant all on <name>.* to 'peter'@'%';`
1. Apply the password function to the string 'readyplayerone' (we'll go over the syntax of this in another lab):<BR>`select password('readyplayerone');`
1. Create a table called `workers` with the following fields (all capital):

Column Name | Description
------------|--------------
EMPLOYEE    | The person's name
MANAGER     | The manager's name
JOB         | A job title
SALARY      | a yearly salary
YEARS_WORKED| The number of years worked

You get to pick the data-types for the fields

1. Enter data that satisfies ALL of the following restrictions into your table:
  *  Roberts, Ruskin, and Raphael are all ticket agents.
  *  Rayburn is a baggage handler.
  *  Rice is a flight mechanic.
  *  Price manages all ticket agents.
  *  Powell manages Rayburn.
  *  Porter manages Rice, Price, Powell and himself.
  *  Powell is head of ground crews and Porter is chief of operations.
  *  Every employee receives a 10% raise for each complete year worked.
  *  Roberts, Ruskin, Raphael, and Rayburn all started at $12,000. Roberts just started work, Ruskin
and Raphael have worked for a year and a half, and Rayburn has worked for 2 years.
  *  Rice started at $18,000 and now makes $21,780.
  *  Price and Powell started at $16,000 and have both been working for three years.
  *  Porter started at $20,000 and has been around two years longer than anyone else.

## Checklist of things to do

* Play around a bit, logged in as temp_admin (see above)
* Watch the Video <http://theurbanpenguin.com/wp/index.php/understanding-mysql-users/>
* Pick username, create the user, create your database, and give yourself privileges on your new database.
* Record your username in [[MariaDB usernames|mariadb-usernames]]
* Do the Exercise above

##References

Use these references for review and to learn some new things that you'll be needing:
  * data types:
    [https://mariadb.com/kb/en/data-types]
  * Nice intro to creating tables:
    [https://www.digitalocean.com/community/tutorials/how-to-create-a-table-in-mysql-and-mariadb-on-an-ubuntu-cloud-server]
  * Inserting data (contains more than you need right now):
    [https://mariadb.com/kb/en/mariadb/how-to-quickly-insert-data-into-mariadb/]
