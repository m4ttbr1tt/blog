+++ 
draft = false
date = 2020-01-27T12:21:51+02:00
title = "CQRS - how it can simplify your software and your job"
authors = ["Matt Britt"]
tags = ["Design", "Architecture","DDD"]
+++

CQRS can help you break up your solutions into logical parts that reduce the contextual burden, making your software more understandable and your job easier.

<!--more-->

### What is CQRS?

CQRS stands for Command Query Responsibility Segregation. Ok, but what is it and how can it help in making your job as a software engineer easier? There are other benefits, like being able to scale the read side separately, but I want to focus on how it benefits you as a developer writing code.

At it's core CQRS is about splitting out your systems reads and writes, and having clearly delineated modules in your software that are responsible for  each of these elements.

If we had to simplify an explanation, about the work we do as software engineers, we take data from a data source,  display it to our users in some way, and then save changes back to our data source.  Sounds simple but there is complexity in business and domains that can quickly get unwieldy, so any means of reducing the cognitive complexity of any piece of code you are working on, can be very beneficial.

### Halve your contextual understanding requirement

Whenever you are working on a piece of code, you need to know the context you are working in, if you can reduce this requirement by half, this makes a huge difference in the effort required to understand what needs to change (and where) and the time it takes to complete the work. You are either working on the read side (components to retrieve and display the data) or the write side (going through the domain to validate your business invariant's)

If you had to embrace CQRS completely, you would separate data sources for the read and write side, synced by streams, events or etl. But you can gain the benefits in your code without these complexities. Arranging your project by logical read and write data, still using a single physical data source, is a great start.

> You can gain the benefits of CQRS without the infrastructural complexities associated with it (eg read and write DBs)

![cqrs](/posts/cqrs-how-it-can-simplify-your-software-and-your-job/cqrs.png)

This single pattern will make a huge difference in how code gets organise, both in your editor, and in your head.
