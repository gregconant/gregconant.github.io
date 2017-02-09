---
layout: post
title:  "The Necessity of Abstractions"
date:   2016-03-30 12:00:00 -0500
categories: general design abstraction
---

We've been dealing with some pretty old code lately. And by "lately", I mean "always". As a result, we have frequent discussions about refactoring (and, jokingly, rewriting the entire thing). Which, I think we can all agree, is rarely possible or realistic.

I've been encouraging the team to take more of a "do what we can to write tests around this, even if it's ugly" mentality, because I don't see another way to get the codebase to improve.

It's been generally agreed that you should write tests around your legacy code as you're refactoring it, assuming those tests don't already exist, so you know that your refactoring won't break anything.

In addition to making your code able to be tested, it should accomplish the additional benefits of making your code more loosely coupled, and more readable.

From a high level, this is done by adding abstractions. One of the biggest problems with legacy code is often is misuse (or complete lack) of abstractions.

You'll often see some server-side code that contains the entire business case in one giant function, from handling UI logic down to persisting to the database. This tight coupling makes it much harder to test, and makes it so you have to understand every line of the function in order to work on any of it.
  
When we were discussing a case like this, and I recommended refactoring some of this logic into separate classes to test against, one of my teammates dismissively responded with something along the lines of, "yeah, great. More abstractions. Is our job to add more complexity?"

This blew my mind. I think creating abstractions is the only way to understand large or complicated applications. Limiting how much you need to know at any time so you know what to focus on is crucial to solve problems. Otherwise you spend valuable time trying to figure out what some confusing "for" loop is doing that could be completely unrelated.

A colleague at a former employer also verbally opposed the idea of putting logic in a clearly-named function, instead preferring to put all the code in that function inline with the rest of the complex logic. This person seemed to prefer to keep all of the logic in his head at once instead of "jumping around" to different functions and methods.

Maybe I'm just not smart enough to keep that much in my head at once. But I find it so much easier to be able to compartmentalize problems and only worry about certain parts of the code at a time.

I once heard someone advocate that all your code in a function should be at about the same level of abstraction and domain logic. So if you execute "DoABusinessThing()", you shouldn't then have, at the same level, a fiddly loop that handles lists or array indexes and does some math. Even if you just push that into a function that you give a clearer name to, the reader can keep only the high-level steps in mind without worrying about implementation details.

It surprises me that a developer would be so opposed to introducing abstractions. If you think that introducing abstractions adds complexity instead of reducing it, your abstractions are probably not very abstract at all.
