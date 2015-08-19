---
title: Review of Khan Academy's Intro to SQL
date: 2015-08-19 13:37 UTC
tags: review, SQL
---

I recently took some time off of working on new projects to hone some of my fundamental skills. As part of that, I decided to take Khan Academy's <a href="https://www.khanacademy.org/computing/computer-programming/sql" target="_blank">Intro to SQL: Querying and managing data</a> course.

### What I learned
In my Rails projects, I've relied pretty heavily on Active Record's magic to generate SQL queries. For example, if I created a relationship in which <code>company</code> has many <code>employees</code> and an <code>employee</code> belongs to a <code>company</code>, I would take for granted that calling <code>company.employees</code> would return an Active Record object with all of the company's employees and that calling <code>employee.company</code> would return the company object. This course taught me how to write the types of relational queries that Active Record calls in the background using <code>JOIN</code>.

The course was also a good refresher on relational databases more broadly. While <code>has_and_belongs_to_many</code> in Rails provides many-to-many relationships easily, the course goes over designing databases with many-to-many and one-to-many relationships. It also covers self-joins, which define relationships between different records in the same table -- e.g. if an <code>employees</code> table has a <code>supervisor_id</code> column that references other employees' primary keys, we can write a query that <code>JOIN</code>s the table with itself to return each employee and his or her supervisor.

### What I liked
The course moved along at a pace that kept me engaged the entire time. It's broken down into short, narrated code demonstrations, which are each followed up by challenges that teach the skill learned in the previous demonstration. The order of the course is intuitive, with the later challenges building off of the concepts from earlier challenges.

Each section of demonstrations and challenges culminates in an open-ended project, which required that I use at least some of the skills I developed in that section but allowed me to play around a bit more. I liked that while the challenges asked me to answer very specific questions, the projects allowed me to define the questions interested me and to answer them.

The entire course is completed in a web browser, which meant I didn't need to spend time setting up my environment. I was also able to work on the course from multiple computers, and Khan Academy remembered where I left off.

### What I didn't like
There were a number of instances when I felt like there was too much hand holding in this course. The instructions for the individual steps of each challenge were so specific that it didn't leave much room for critical thinking or problem solving. And each step immediately provided a "hint" with specific syntax before I asked for it. In general, I wish that I was given more time to think and struggle on my own.

The in-browser workspace had a couple of quirks. For one, every time I stopped typing for just a couple seconds, the system would check my code for errors and if it found one it would pop up a little mascot telling me so, à la Microsoft Word's Clippy. I wish that it would have waited until I clicked a button before it evaluated my code. Also, being in a browser window, if I accidentally clicked outside of the code editor and hit the backspace key, it would send me back to the last page I was on, and it didn't always save my progress.

The narration was a mixed bag. It was upbeat, friendly, and unintimidating, but there were times when it felt too childish.

### Conclusion
Overall, I enjoyed the course. It was pretty much what I expected -- a quick, hands-on tutorial on SQL queries and relational databases. I'd recommend it to others who have relied on a framework for interacting with databases and could use a refresher on some of the underlying mechanisms.
