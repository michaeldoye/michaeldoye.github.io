---
layout: post
title: Cpanel chron job to backup MySql and send to email
published: false
tags: 
  - php
---
I had an idea to for (semi) redundancy for one of our larger projects - an ECommerce store running on a  WordPress installation.

In a nutshell the idea involves duplicating the account on another server and when receiving a notification from a monitoring tool that the server is down, we would change the domains `A` record to point to the backup server. There would still be few minutes of downtime but it would be minimal.

I realized I would need some way of synchronising the the MySql databases between the production and back-up servers. This would be pretty important, considering the type of website - order numbers etc.

The site runs on Linux, with CentOS and Cpanel.  Through the Cpanel interface you can schedule cron Jobs that will run a given command. This would be perfect for getting a backup of the MySql database and saving it off-site.

![image of chron here](http://i.imgur.com/fV2obi3.png)

I decided to start out some basic testing using a handy PHP script ([which I found here](https://www.maketecheasier.com/schedule-database-backup-using-cron-job/)) that will run the back up and send it to me via email at a time interval of my choosing.  This is obviously not a secure way of sending potentially sensitive data (I will have it saved to the backup server via FTP once in production).

However I thought this would be something that other people would find quite useful to send less sensitive data.

I will eventually have a python script that gets executed to do the MySql sync. 
In the meantime, here is how to do it using PHP.

Firstly, you will want to download the PHP from [here](https://github.com/michaeldoye/ballin-octo-wookie). Then edit the backup.php file to include your details:

```javascript

// edit this section
$dbhost = "localhost"; // usually localhost
$dbuser = "dbuser"; //enter your database username here
$dbpass = "dbpass"; //enter your database password here
$dbname = "dbname"; // enter your database name here
$sendto = "Send To <sendto@email.com>";
$sendfrom = "Send From <sendfrom@email.com>";
$sendsubject = "Daily Database Backup";
$bodyofemail = "Here is the daily backup of my database.";
```

You will need to then upload these files to your server so that you can point a command to it. In Cpanel, create a new cron job:

![New cron](http://i.imgur.com/B5nPBqq.png)

Choose your desired interval and in the command field type the following command:

`/user/bin/php -q /home/account/public_html/path-to-folder/backup.php`

Set your time interval for close to whatever time it is now so you can test that it is working.




