---
title: Tracking loans and payments between users
date: 2015-08-04 11:06 UTC
tags: Ruby, Rails
---

Tools like Venmo and PayPal have made it easier to quickly send money to friends when cash isn't convenient. For folks like roommates, with whom you have a lot of shared costs each month, it may make more sense to keep track of what you owe each other over time and settle up on a regular interval, rather than making many smaller transactions.

I'm working on a Rails application that would allow friends to help track this type of arrangement. The core functionality of the app will be tracking the loans and payments between users and tallying them up.

One interesting challenge about tracking these loans and payments is that each transaction has to be reflected for both users but in opposite directions. For example, if a <code>user_a</code> loans <code>user_b</code> $10, the app must be able to tell <code>user_a</code> that he is owed $10 from <code>user_b</code> and tell <code>user_b</code> that he owes $10 to <code>user_a</code>. I set out to accomplish this in a way that would be intuitive and avoid duplicating transactions in the database. I landed on creating two models — User and Transaction — with a schema that looks like this:

![Database schema for my Rails app](tracking_loans_and_payments_schema.svg)

In the User and Transaction models, I set up some relationships: a transaction belongs to <code>from</code> and <code>to</code>, which are each users, and a user has many <code>from_transactions</code> and <code>to_transactions</code>. With two instance methods in the User model, I can track and total loans and payments between users.

~~~ruby
class User < ActiveRecord::Base
  has_many :from_transactions, foreign_key: "from_id", class_name: "Transaction"
  has_many :to_transactions, foreign_key: "to_id", class_name: "Transaction"

  def transactions_with(friend)
    from_transactions.where(to_id: friend.id) + to_transactions.where(from_id: friend.id)
  end

  def owes(friend)
    owes = 0
    from_transactions.where(to_id: friend.id).each { |transaction| owes -= transaction.amount }
    to_transactions.where(from_id: friend.id).each { |transaction| owes += transaction.amount }
    owes
  end
end
~~~

Calling <code>user_a.transactions_with(user_b)</code> returns all of the transactions between <code>user_a</code> and <code>user_b</code>, both when <code>user_a</code> is the <code>from</code> user and when he is the <code>to</code> user. In a future iteration of this method, I'll sort the transactions so that the newest ones are returned first, but this serves as a good starting point.

Calling <code>user_a.owes(user_b)</code> returns the amount <code>user_a</code> owes <code>user_b</code> by subtracting each transaction on which <code>user_a</code> is the <code>from</code> user and adding each transaction on which he is the <code>to</code> user. For a loan, this means when <code>user_b</code> makes a $5 loan <code>to user_a</code>, <code>user_a.owes(user_b)</code> increases by $5 and <code>user_b.owes(user_a)</code> decreases by $5. <code>user_a</code> could settle his debt by making a payment back <code>to user_b</code>, which would decrease <code>user_a.owes(user_b)</code> and increase <code>user_b.owes(user_a)</code> by $5.
