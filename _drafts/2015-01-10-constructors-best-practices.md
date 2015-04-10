---
layout: post
title:  "Constructors Best Practices"
date:   2015-01-10 20:00:00
categories: ["Constructors, Clean Code"]
tags: 	["Constructors, Clean Code"]
---
A few things I picked up from one of my mentors regarding Constructors.

##Constructors should not result in an exception instantiating a type
should not contain logic
user gaurds instead and throw from there 

##You should have an overloaded constructor that allows the type's members to be set

##Simplify the number of constructors (chaining) by taking advantage of default parameters