---
layout: post
title:  "TDD and Unit Testing Without Mocks"
date:   2014-07-09 17:33:05
categories: [TDD, "Unit Testing"]
tags: 	[TDD, Mocks, "Unit Testing"]
comments: true
---

About a month or so ago, on a trip to a conference, during dinner, my colleagues and I had a conversation around the use of mocks in unit testing. Not surprisingly it was a passionate conversation on the premise that mocks are bad and should be avoided because it can lead to poor testing form and ultimately sub-optimal design. The conversation was not only entertaining but very interesting, so I took some time to look into it further and form my own opinions.

**First off, why are mocks bad?**

Well mocks are not necessarily bad. In fact, mocks not only help us be more agile by allowing us to program against dependencies that don't exist, but they also help us isolate our code from their dependencies. These are good things right? Absolutely! However there are a few problems with mocks you may be unaware of.

**'Mock' is an overloaded term.**
The term mock is confusing. Mock can have different meanings depending on what we are trying to do. For example, deciding on the approach we take for our tests (state-based vs. interaction based). Many frameworks allow you to use mocks as stubs and vice-versa. Some do make a clear distinction while others don't. Either way, the term is confusing and can make writing unit tests more difficult.

**Mocks deemphasize state-based testing.**
An over reliance on mocks, especially when practicing TDD can lead toward writing more interaction based tests. The higher the number of interaction based tests the more brittle your tests are likely to be. Also, chances are the more interactions you have, the tighter the coupling between the dependency and your SUT. Which brings us to our last problem with mocks.

**Mocks are a smell.**
Conveniently, mocks allow us to easily traverse dependencies; however, there mere presence can be an indicator of sub-optimal design. Writing tests first certainly does help write code that is testable by reducing the number of concrete dependencies our code might have, but just because we replace a concrete dependency with an interface, does not mean our design is optimal. We are better off, but blindly implementing mocks with disregard for proper design, influences our design process in a manner that leaves us with a sub-optimal design. 

**So how do we test without Mocks?**

Well I'm not a proponent of completely abandoning mocks. I think this is extreme and unrealistic. After all there are some benefits; however, we should consciously reduce our dependencies on mocks (no pun intended) by defaulting to state-based tests over interaction based tests. Make state-based testing the default. In other words, focus on outcomes. It may help to keep in mind that you should be asserting your tests against outcomes. If you find yourself asserting against mocks then your unit test is probably an interaction based test. Testing interactions is better suited for integration tests; this is broad statement -- it really depends on your goals, but I'm trying to drive a point that you should have few interaction based tests.

Below are some posts that I thought were insightful.

* [Stop Using Mocks](http://openmymind.net/2011/3/23/Stop-Using-Mocks/)
* [Back to Basics. Unit Testing Without Mocks](http://simpleprogrammer.com/2011/01/23/back-to-basics-unit-testing-without-mocks/)
* [The No Mocks Book](http://arlobelshee.com/the-no-mocks-book/)
* [Goodbye Mocks. Farewell Stubs](http://osherove.com/blog/2008/9/20/goodbye-mocks-farewell-stubs.html)
