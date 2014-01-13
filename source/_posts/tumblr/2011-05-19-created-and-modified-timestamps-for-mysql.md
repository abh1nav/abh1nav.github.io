---
layout: post
title: Created and Modified timestamps for MySQL
date: '2011-05-19T00:00:00-04:00'
tags:
- MySQL
- CURRENT_TIMESTAMP
- last modified
- last updated
- multiple timestamps
- mysql triggers
- triggers
tumblr_url: http://abhinav.ca/post/6202244318/created-and-modified-timestamps-for-mysql
---
A fairly common requirement for database tables is to have a created and last modified timestamp for each record. This seems to be yet another spot where something fairly straightforward requires you to take the scenic route to implement. Ordinarily, the create table statement would look like:
{% codeblock lang:mysql %}
CREATE  TABLE `mydb`.`mytable` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT ,
  `myfield` VARCHAR(255) NOT NULL ,
  `modified` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE NOW() ,
  `created` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`) )
ENGINE = MyISAM;
{% endcodeblock %}

And that’s when MySQL threw me a nice
{% codeblock %}
Incorrect table definition; there can be only one TIMESTAMP column with CURRENT_TIMESTAMP in DEFAULT or ON UPDATE clause.
{% endcodeblock %}

Upon digging a little further, I found this little nugget in the documentation:

{% blockquote MySQL Docs http://dev.mysql.com/doc/refman/4.1/en/timestamp.html %}
For one TIMESTAMP column in a table, you can assign the current timestamp as the default value and the auto-update value. It is possible to have the current timestamp be the default value for initializing the column, for the auto-update value, or both. It is not possible to have the current timestamp be the default value for one column and the auto-update value for another column.
{% endblockquote %}

Well isn’t that just peachy.  
  
**The Solution**  
  
To overcome this hurdle, I employed the use of a trigger for the created timestamp and decided to leave the modified as is. The create table statement then turned into:
{% codeblock lang:mysql %}
CREATE  TABLE `mydb`.`mytable` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT ,
  `myfield` VARCHAR(255) NOT NULL ,
  `modified` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE NOW() ,
  `created` TIMESTAMP NULL,
  PRIMARY KEY (`id`) )
ENGINE = MyISAM;
{% endcodeblock %}

And used the following trigger to process the created timestamp -
{% codeblock lang:mysql %}
USE `mydb`;
DELIMITER $$
	CREATE TRIGGER trigger_mytable_before_insert BEFORE INSERT ON `mytable`
	FOR EACH ROW BEGIN
		SET NEW.created = NOW();
	END;
$$
{% endcodeblock %}
