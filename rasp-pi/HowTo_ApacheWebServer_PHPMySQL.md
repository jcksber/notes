
How To Install a Web Server on the Raspberry Pi (Apache + PHP + MySQL)
======================================================================
> Written by Jack Kasbeer [03.04.19 : 16:56]
*Reference documentation:*
[How to install a web server on Raspberry Pi with PHP + MySQL](https://howtoraspberrypi.com/how-to-install-web-server-raspberry-pi-lamp/)
[How to Host a Website with Raspberry Pi](https://readwrite.com/2014/06/27/raspberry-pi-web-server-website-hosting/)

After creating your SD card, and after starting your Raspberry Pi for the first time, there are strong chances that you want to use as a web server.


Setting Up the Pi
-----------------
Three things are needed after unboxing the Pi:
1. **A router and modem.** It may seem obvious, but you'll need a router and a modem from your Internet service provider. A separate router would give you more flexibility to connect multiple devices.
2. **An Ethernet cable.** Or, less recommend, a wireless USB adapter. Either way, you'll need the Pi to have a permanent Internet connection.
3. **An operating system.** I recommend Raspbian. In order to set up an operating system on your Pi for the first time, [look at my tutorial](https://readwrite.com/2014/01/20/raspberry-pi-everything-you-need-to-know/#awesm=~oHv8Sla0tb21Mo).
4. **SSH (Secure Shell) access.** A Web server doesn't need a keyboard, monitor, or mouse. Instead, you'll access the Pi remotely through your laptop or another device that has those things. 
[Set up SSH for the first time](https://readwrite.com/2014/04/09/raspberry-pi-projects-ssh-remote-desktop-static-ip-tutorial/#awesm=~oHt6ZNCfyofCiA).


Why a Raspberry Pi as a web server?
-----------------------------------
But **why use a Raspberry as a web server**, rather than using service providers specialized in web hosting

*For the case of CoinFlip, it's a perfect way to host a site "offline" that gathers extremely important LIVE DATA to display to the Operations and Customer Support teams.*  From an economic point of view, it's obvious.  However, a Raspberry Pi will always be constrained by memory & RAM, whereas commercial products will not.  The Raspberry Pi 3 comes with 1 GB of RAM, whereas the Raspberry type B+ only comes with 512 MB of RAM.

**How to make a web server on Raspberry Pi** given that we, hypothetically, have no money, a site ~200 MB in size, and a database that we don't need to store on the RaspPi itself? **Installation du serveur Apache avec Raspbian!**

### What is Apache?
First, we will install Apache (the flavor web server we've chosen for the project).  Don't blindly think to yourself "machine" when you read *web server*: **this term also refers to the software** allowing and instructing that machine when and how to run (analyze user requests & return the file/error corresponding to the request.

At the moment, **Apache is the most used web server,** with about 60% market share.  Apache has its own license, used by many other projects.  In addition, the massive use of Apache, coupled with its high popularity, has led to a tremendous abundance of documentation, courses, and other books dealing with its use, and security.

Whether it is for Raspberry Pi and Raspbian, or for a more general-purpose machine, **Apache is therefore a safe choice,** and the skills you will be able to acquire on the subject will always be useful.

### Apache installation
Before installing the server, make sure we have an up-to-date machine. To do this **must have administrator rights,** either because of the sudo command.
```bash
sudo apt update
sudo apt upgrade
sudo apt update
```
Once the Raspberry Pi is up to date, we will install the Apache server.
```bash
sudo apt install apache2
```
By the way, we'll take advantage of it to give rights to the apache file that you can easily manage your sites. To do this, run the following commands:
```bash
sudo chown -R pi:www-data /var/www/html/
sudo chmod -R 770 /var/ww/html/
```

### Check if Apache is working
Once the installation completed, we can **test that Apache is working properly** by going to the Raspberry address.
To do this, it's necessary to the Raspberry from port 80 (this port, not being opened from the outside, we'll have to do this on the Raspberry itself).
Do not worry, it's very easy. Simply open the Raspberry web browser, and go to "http://127.0.0.1". You should then get a page with a message like "It works!" and plenty of other text.
If you do not already have a GUI on your Raspbian, or you use SSH to connect to your Raspberry, you can use the following command:
```bash
wget -O check_apache.html http://127.0.0.1
```
This command will **save the HTML code of the page in the file "check_apache.html"** in the current directory.
So you only have to read the file with the command
```bash
cat ./check_apache.html
```
If you see marked at a location in the code "It works!", then Apache is working.

**Apache uses the directory "/var/www/html" as the root** for your site.  This means that when you call your Raspberry on port 80 (http), Apache looks for the file in "/var/www/html".
For example, if you call the address "http://127.0.0.1/example", Apache will look for the "example" file in the "/var/www/html" directory.
To add new files, sites, etc., you will need **to add them to this directory.**

You can now use your Raspberry to make a site in **HTML, CSS and JavaScript,** internally.
However, you may want to quickly **allow interactions between the site and the user.** For example, to allow the user to register, etc. For this, you are going to need PHP.


PHP installation on your Raspberry Pi
-------------------------------------

### What is PHP?
First of all, you should know that **PHP is an interpreted language.** And as in the case of servers, the acronym PHP **can have several meanings.** In fact, when we talk about PHP, we can talk about **either the language or the interpreter.**
Here, when we talk about installing PHP, it means that **we will install the interpreter,** in order to use the language.

PHP (the language this time) is mainly used to make a site dynamic, that is to say that the user sends information to the server which returns the **modified results according to this information.**
Conversely, a static site **doesn't adapt to information provided by a user.** It's saved as a file once for all, and will always deliver the same content.

**PHP is free,** and maintained by the PHP Foundation, as well as Zend Enterprise, and various other companies (it should be noted that Zend is also the author of the famous Zend PHP framework, widely used and recognized in the world of "business").

It's one of the most widely used programming languages, and it is even the most used for web programming, with about 79% market share.

Again, all the skills you can acquire, on the language, or on the installation and configuration of the interpreter, will always be useful. So we can only advise you to learn PHP, which is really a wonderful language and too often underestimated.

### How to install PHP
We will again use the administrator to install PHP with the command line.
```bash
sudo apt install php php-mbstring
```

### Check if PHP is working
To know if PHP is working properly, it's not very complicated, and the **method is quite similar to the one used for Apache.**

You will first **delete the file "index.html"** in the directory "/var/www/html".
```bash
sudo rm /var/www/html/index.html
```
Then create an "index.php" file in this directory, with this command:
```bash
echo "<?php phpinfo ();?>" > /var/www/html/index.php
```
From there, **the operation is the same as for the Apache check.** You try to access your page, and you should have a result close to this image (if you do not have an interface, use the same method as before, and look for the words "PHP Version"). Or use `phpinfo` command on a raspberry.


A MySQL database for your server
--------------------------------

### A **DBMS**, what is that? Why MySQL?
Now that we have set up PHP, you will probably **want to store information** for use in your sites. For this purpose, databases are most often used.
We will therefore set up a DBMS (Database Management System), namely MySQL.

**MySQL is a free,** powerful, massively used DBMS (about 56% market share of free DBMS). Here again, MySQL is so essential to development, whatever the language, that you must absolutely learn and master it, with [this book for example](https://www.amazon.com/Learn-MySQL-Plain-English-Beginners-ebook/dp/B00OVB9ULY/ref=as_li_ss_tl?tag=raspbianfrace-20&ie=UTF8&qid=1482853011&sr=8-6&keywords=mysql&linkId=4e38c06c22b6fff8d22a97bf5a7b21f8).

### How to install MySQL
To do this, we will install mysql-server **and** php-mysql (which will serve as a link between php and mysql)
```bash
sudo apt install mysql-server php-mysql
```

### Verify that MySQL is working correctly
This time, to check, **we will only use the command line.** To do this, we will simply connect via the command:
```bash
sudo mysql --user=root
```
We will not delete the default mysql root user and create a new mysql root user. The default one can only be used with Linux root account, and so not available for the web server and php scripts.

To do so, once you connect to MySQL, simply run these commands (replace `password` with the password you want):
```sql
DROP USER 'root'@'localhost';
CREATE USER 'root'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVELEGES ON *.* TO 'root'@'localhost'
```
So you now have **a web server, connected to PHP and MySQL.** That's all it takes.

(On your next connections, you will be able to connect to mysql without using sudo, with the command `mysql --user=root --password=yourmysqlpassword`).

### Add PHPMyAdmin
The installation of PHPMyAdmin is absolutely not necessary. **In this installation, we will not take care about any special security settings!**

The PHPMyAdmin installation is pretty quick and easy, we simply have to use the packet manager with this command:
```bash
sudo apt install phpmyadmin
```
PHPMyAdmin installation program will ask you a few questions. For the `dbconfig-common` part, choose to not use it (as we have already configured our database). For the server choice to configure PHPMyAdmin, choose Apache. And the root password is the one you set for MySQL.

### Check that PHPMyAdmin is working properly
To check, you will simply try to access it, using the address of your Raspberry followed by /phpmyadmin. For example, locally it will be `http://127.0.0.1/phpmyadmin`.

If you still get an error, it could be because PHPMyAdmin has moved to another directory. In this case, try the command
```bash
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```
Now, we can access to PHPMyAdmin from Raspberry Pi's browser, with the url: `http://127.0.0.1/phpmyadmin`

Making a server accessible from the web
---------------------------------------
Your server is ready. However, **you probably cannot access it from the internet.** Indeed, it would be necessary for your modem to redirect the requests to your Raspberry (the good ports).

To put these redirections in place, and even get a URL, you should look to DynDNS and port forwarding!

