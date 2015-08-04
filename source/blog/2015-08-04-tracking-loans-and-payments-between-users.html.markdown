---
title: Tracking loans and payments between users
date: 2015-08-04 11:06 UTC
tags: Ruby, Rails
---

Tools like Venmo and PayPal have made it easier to quickly send money to friends when cash isn't convenient. For folks like roommates, with whom you have a lot of shared costs each month, it may make more sense to keep track of what you owe each other over time and settle up on a regular interval, rather than making many smaller transactions.

I'm working on a Rails application that would allow friends to help track this type of arrangement. The core functionality of the app will be tracking the loans and payments between users and tallying them up.

One interesting challenge about tracking these loans and payments is that each transaction has to be reflected for both users but in the opposite transaction. For example, if a User A loans User B $10, the app must be able to tell User A that he is owed $10 from User B and tell User B that he owes $10 to User A. I set out to find a way to accomplish this in a way that would be intuitive and avoid duplicating transactions in the database. I landed on creating two models — User and Transaction — with a schema that looked like this:

![Database schema for my Rails app](opentab3.svg)
