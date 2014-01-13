---
layout: post
title: Dating MySQL in Python
date: '2011-05-15T00:00:00-04:00'
tags:
- MySQL
- Python
- SQL Server
- development
- database
- DateTime
- Python.DateTime
- ADO.NET
- CSharp
- date handling
tumblr_url: http://abhinav.ca/post/6202164319/dating-mysql-in-python
---

I’ve always found it difficult to wrap my head around the various language date implementations because each one has its idiosyncrasies. Each time I have to work with a new language, I have to re-learn their date handling constructs, which is a bit of a pain. Databases are the worst culprits in this respect because each database offers a date, datetime or timestamp field and each database engine has its own expectation for how that date time field should be queried. SQL Server and C# work together to simplify matters a bit (or so I thought). Consider the following sample:
{% codeblock lang:csharp %}
string queryString = "Data Source=sqladdr; Initial Catalog=mydb; User Id=usr; Password=pwd;";
SqlConnection conn = new SqlConnection(queryString);
conn.Open();
SqlCommand cmd = new SqlCommand(conn);
cmd.CommandText = "INSERT INTO tblTest (currDate) VALUES (@DateParam)";
SqlParameter param = new SqlParameter();
param.ParameterName = "@DateParam";
param.Value = DateTime.Now;
cmd.Parameters.Add(param);
cmd.ExecuteNonQuery();
conn.Close();
{% endcodeblock %}
Today I learned, by delegating the task to a friend, that Python exposes the MySQL datetime in a much easier-to-grasp manner. A sample date interaction in python would be:
{% codeblock lang:python %}
from datetime import *
import MySQLdb
 
conn = MySQLdb.connect(
		host="dbhost",
        user="username",
        passwd="pwd",
        db="schema")
cursor = conn.cursor()
cursor.execute("SELECT * FROM tblTest")
row = cursor.fetchone()
if (row is not None):
	# the field index according to the select statement
    date_value = row[0] 
# here, date_value is a datetime object. automatically. :D
cursor.execute("INSERT INTO `date_table` (`the_date`) " + \
               "VALUES (''%s'')" % (date_value.isoformat()))
# and just like that, you''re done.
conn.close()
{% endcodeblock %}
All it takes is to enclose the date in quotes and invoke the `date_object.isoformat()` method, and voila, you’re done. What can I say, I’m really starting to dig python.
