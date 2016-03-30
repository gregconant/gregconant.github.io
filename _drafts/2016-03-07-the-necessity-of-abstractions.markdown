---
layout: post
title:  "The Necessity of Abstractions"
date:   2016-03-07 12:00:00 -0500
categories: general design
---

We've been dealing with some pretty old code lately. And by "lately", I mean "always". As a result, we have frequent discussions about refactoring (and, jokingly, rewriting the entire thing). Which, I think we can all agree, is rarely possible or realistic.

I've been encouraging the team to take more of a "do what we can to write tests around this, even if it's ugly" mentality, because I don't see another way to get the codebase to improve.

It's been generally agreed that you should write tests around your legacy code as you're refactoring it, assuming those tests don't already exist, so you know that your refactoring won't break anything.

In addition to making your code able to be tested, it should accomplish the additional benefits of making your code more loosely coupled, and more readable.

From a high level, this is done by adding abstractions. One of the biggest problems with legacy code is often is misuse (or complete lack of) abstractions.

You'll often see some server-side code that contains the entire business case in one giant function, from handling UI logic down to persisting to the database. This tight coupling makes it much harder to test, and makes it so you have to understand every line of the function in order to work on any of it.
  
When we were discussing a case like this, and I recommended refactoring some of this logic into separate classes to test against, one of my teammates dismissively responded with something along the lines of, "yeah, great. More abstractions. Is our job to make our jobs harder?"