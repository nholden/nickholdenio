---
title: One difference between SQLite and PostgreSQL revealed through continuous integration
date: 2015-07-23 09:44 UTC
tags: Ruby, Rails, SQL, RSpec
---

For a Rails application I'm working on, I use [Codeship](http://www.codeship.com){:target="_blank"} for continuous integration. This means that whenever I make a new commit to my master branch and push it to Github, Codeship runs my entire test suite and pushes my application to production on Heroku if all the tests pass. This makes it easy to keep my production environment updated with my latest features, and it also catches bugs that don't appear in development.

One of the big differences between my development and production environments is that my application uses SQLite in development and PostgreSQL in production. Because of Active Record's magic, I don't notice this very often. However, a difference between SQLite and PostgreSQL caused this RSpec test on my User model that passed in development to fail when Codeship ran the test in production.

~~~ruby
it "should include users who are Facebook friends" do
  expect(@user.friends).to include(@friend_from_facebook)
end
~~~

In this test, <code>@user</code> and <code>@friend_from_facebook</code> are users who are also friends on Facebook. When Codeship ran the test in production, I received an error.

~~~
ActiveRecord::StatementInvalid:
  PG::UndefinedFunction: ERROR:  operator does not exist: character varying = bigint
  LINE 1: SELECT "users".* FROM "users"  WHERE ((uid IN (1234567890123...
                                                     ^
  HINT:  No operator matches the given name and argument type(s). You might need to add explicit type casts.
  : SELECT "users".* FROM "users"  WHERE ((uid IN (1234567890123456) AND provider = 'facebook') OR
                 (created_by_user_id = 88))
~~~

I took a look at the offending Active Record method call in my <code>User</code> model.

~~~ruby
User.where("(uid IN (#{facebook_friends_uids.join(', ')}) AND provider = 'facebook') OR
                     (created_by_user_id = #{self.id})")
~~~

When called on a user, the <code>friends</code> method is supposed to return an array of that user's friends. This call on Active Record's <code>where</code> method intended a user's friends to include both his Facebook friends and also the other users whom that user created. It generates a SQL query like this.

~~~sql
SELECT "users".* FROM "users"  WHERE ((uid IN (1234567890123456) AND provider = 'facebook') OR (created_by_user_id = 88))
~~~

In the database schema, <code>uid</code> and <code>provider</code> are strings and <code>created_by_user_id</code> is an integer. As it turns out, omitting quotes around the <code>uid</code> in this SQL query does different things in SQLite and PostgreSQL.

In SQLite, <code>1234567890123456</code> is an [integer](https://www.sqlite.org/datatype3.html){:target="_blank"}, which it automatically converts into a string when it receives the query. In PostgreSQL, the same number is a [bigint](http://www.postgresql.org/docs/9.4/static/datatype-numeric.html){:target="_blank"}, which it does not convert into a string. 

I added quotes around the <code>uid</code> in my Active Record method call so that it would pass a string in the SQL query rather than an integer or bigint.

~~~ruby
User.where("(uid IN ('#{facebook_friends_uids.join(', ')}') AND provider = 'facebook') OR
                     (created_by_user_id = #{self.id})")
~~~

After the change, I made a new commit to my master branch and pushed it to Github. My test suite passed on Codeship, and Codeship automatically pushed my code to production.
