---
layout: post
title: MySQLdb - why you no INSERT data?!
date: '2011-06-05T01:00:00-04:00'
tags:
- MySQL
- Development
- Python
- MySQLdb
- python-mysqldb
- SQL Python INSERT
- storage engines
- InnoDB
- git
- git status
tumblr_url: http://abhinav.ca/post/6202422928/mysqldb-why-you-no-insert-data
---
I was using MySQLdb to interact with a MySQL server in Python. The script did some selects and some inserts, nothing fancy. All of a sudden, the selects are working, but the inserts refuse to stick! The script source hadn’t been touched, far as I could tell.  

Despite my faith in `git status`, after meticulously going through each line of code (to ensure absence of PEBKAC), I determined that the script really hadn’t been touched.  
  
Finally remembered, halfway through a new script, I had decided to switch the storage engine from **MyISAM to InnoDB** and therein, as the Bard would tell us, lies the rub.  
Python-MySQLdb needs an additional connection.commit() statement for all the inserts to actually go through to InnoDB!  

{% codeblock lang:python %}
connection = MySQLdb.connect( 
			     host = dbHost, 
			     user = dbUsername,
			     passwd = dbPassword,
			     db = dbSchema);
cursor = connection.cursor()
sql = "INSERT INTO ..."
cursor.execute(sql)
cursor.close()
 
# Extra step for InnoDB
connection.commit()
 
connection.close()
{% endcodeblock %}