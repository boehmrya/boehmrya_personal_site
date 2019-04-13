---
layout: post
title:  "Common and Useful MySQL Commands"
date:   2019-04-09 01:32:52 -0400
categories: MySQL
---

Here is a list of some common and useful sql commands, since i'm messing around with this today.

I always keep looking these up, so this new blog is a good place to catalog them.

Login to MySQL:

{% highlight ruby %}
mysql -u root -p
{% endhighlight %}

You'll be prompted for your password.  Enter it.

Next, view the existing databases.

{% highlight ruby %}
SHOW DATABASES;
{% endhighlight %}

A list of all of the existing databases will appear.

Create a Database:

{% highlight ruby %}
CREATE DATABASE testdb;
{% endhighlight %}

testdb is the name of the database.  Now, to view the database.

Use the database:

{% highlight ruby %}
USE testdb;
{% endhighlight %}

Grant all permissions to a specific user:

{% highlight ruby %}
GRANT ALL ON testdb.* TO 'your_mysql_name'@'your_client_host';
{% endhighlight %}


Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

GRANT ALL ON drupal_test.* TO root@localhost;
