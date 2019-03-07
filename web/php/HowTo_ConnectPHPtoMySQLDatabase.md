
How to Connect PHP to MySQL Database
====================================
> Written by Jack Kasbeer [03.05.19 : 17:33]
*Reference documentation:*
[How to - connect PHP to MySQL DBMS](https://www.hostinger.com/tutorials/how-to-connect-php-to-mysql)

This tutorial will be very useful for a newb in web development. Using this tutorial, you will learn how to use **PHP** and **connect** to **MySQL** database.
That is needed if you wish to modify, view, delete or manage your created tables in any other way.


What you'll need
----------------
Before you begin this guide you'll need the following:
* Access to your hosting control panel


Step 1 - Creating a MySQL Database
----------------------------------
This step is **required in case you do not have a MySQL database created yet. For instructions how to create a MySQL database on cPanel with Hostinger, see [this tutorial](https://www.hostinger.com/tutorials/mysql/how-to-create-mysql-database-and-user-on-cpanel).

> **IMPORTANT:** Write down details of the MySQL database you just created. You will need them in the next step.


Step 2 - Writing the PHP code for connecting to MySQL database
--------------------------------------------------------------
In this tutorial, we will be connecting to a db with a name **u_name** and this db of ours has an associated user **u_user.** You must as well have a password to the db, in our case the password will be **allez.**

Another thing worth mentioning is that you must have a correct servername or hostname. At Hostinger, MySQL hostname can be found in **MySQL Databases** section. In our example, it is **mysql.hostinger.com**.

> In a lot of cases, when you are connecting to a db locally (the script which is connecting to the db is uploaded to the server where the db exists), you can use **localhost**.  If that does not work, or if you are connecting to a database from a remote location (for example, your computer) you have to use the IP address of the server. For more details, contact your hosting provider so they could provide you with the correct information on what to use as a hostname.

There are now two methods to connect to an SQL database with PHP - they are **MySQLi** and **PDO.** One of the most important changes is that they both support 'prepared statements', which remove the ability for SQL injection attacks when making changes to the database. The original 'mysql-' functions are *deprecated* and should not be used as they are unsafe and no longer being maintained or developed.

**MySSQLi** stands for **MySQL Improved** and adds new features to the MySQL interface specifically. **PDO** stands for **PHP Data Object.** The main difference between PDO and MySQLi is that PDO supports a number of different db types (MySQL, MS SQL, Postgre DB) in the same script, however you only have to write data related functions once. PDO is 'object oriented', the connection to the db is created by creating a variable object.
E.g.:
```
$my__Object = new OBJECT();
```
### MySQLi
So here is the basic PHP code example which can be used to establish a connection to a MySQL db using MySQLi:
```php
<?php
$severname = "mysql.hostinger.com";
$database = "u__name";
$username = "u__user";
$password = "allez";

// Create connection

$conn = mysqli__connect($servername, $username, $password, $database);

// Check connection

if (!$conn) {
  die("Connection failed: " . mysqli__connect__error());
}

echo "Connected successfully";
mysqli__close($conn);
?>
```
The main method used in this script is **mysqli_connect()**. This is an internal PHP function, you can read more about it [here.](http://www.w3schools.com/php/func_mysqli_connect.asp)

At the very beginning of our code, we see few variable declarations and values assigned to those variables. Usually, we need four variables to establish a proper connection: **$servername, $database, $username, and $password.**

The next step is the function **mysqli_connect(),** it tries to establish a connection to a db with the provided details and the next part of the code is executed based on the success of **mysqli_connect()** function. As you can see, we have set that if the connection fails, we get an error message.

A function **die()** is executed here, which basically *kills* our script and gives us a message that we have set. So this will by default say **Connection failed:** and an exact error message will follow to help us determine the issue.

If the connection is successful, next part of the code prints via `echo`.

Finally, close the connection for good practice.

You should be able to run the code now by accessing the script through your domain.

### PDO
A PDO db connection requires you to create a new 'PDO object' with a **Data Source Name (DSN),** username and password. The DSN defines the type of db, the name of the db, and any other information if required. The DSN can be a simple variable which is then used as a parameter when creating the actual PDO object, as shown belown.






























