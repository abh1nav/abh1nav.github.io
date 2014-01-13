---
layout: post
title: Renaming a MySQL database
date: '2011-05-13T00:46:00-04:00'
tags:
- development
- MySQL
- rename database
- SQL Server
- Stack Overflow
tumblr_url: http://abhinav.ca/post/6202052476/renaming-a-mysql-database
---
 
I was surprised to learn that there is no simple way to rename a database in MySQL without bringing the whole server down and renaming data files.  
  
You’d think it would be a trivial task, especially considering the SQL Server docs provide the following procedure to rename a database:  
{% blockquote SQL Server Documentation %}
To rename a database
In Object Explorer, connect to an instance of the SQL Server Database Engine, and then expand that instance.
Make sure that no one is using the database, and then set the database to single-user mode.
Expand Databases, right-click the database to rename, and then click Rename.
Enter the new database name, and then click OK
{% endblockquote %}

The closest I’ve come to finding a non-invasive way is by running the following series of commands (courtesy of Stack Overflow): 
{% codeblock lang:sh %}
mysqldump -u username -p -v olddatabase > olddbdump.sql
mysqladmin -u username -p create newdatabase
mysql -u username -p newdatabase < olddbdump.sql
{% endcodeblock %}
Make sure there are no connections active to the old database at the time as you may not get the freshest data and remember to drop the old database after you’re done.
Original Stack Overflow thread: http://bit.ly/14ETgF
