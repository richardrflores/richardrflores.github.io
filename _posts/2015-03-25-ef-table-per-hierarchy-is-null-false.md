---
layout: post
title:  "EF Table Per Hierarchy (TPH) IsNull = False Error"
date:   2015-03-25 20:00:00
categories: ["Entity Framework", EF]
tags: 	["Entity Framework", EF]
---
Today I had to refactor some domain models to a generalized hierarchy with EF. I used the TPH pattern defined by the EF team to split a JournalEntryHeader into a BillingRecordJournalEntryHeader and an InvoiceJournalEntryHeader in order to secure the domain models using the projects data entitlements paradigm.

Not so fast!

I hit a couple of blocks. The main reason is that we were trying to use two different discriminator columns. I kept getting a “IsNull=False” error. Although syntactically it was implemented correctly, it did not work. I then tried to use a type property that we have that actually would have worked better, but I ran into the same problem. This was troubling because we have this working somewhere else in the solution…More on that later.

A few things I discovered:
1. The EF mapping configuration Map method takes either a string to map the property or a lambda. 
2. If using the string, then the HasValue method must specify a value to use as a discriminator. If using the lambda, then you must use the HasValue method; in this case the HasValue does not require you to specify a discriminator value; I assume you could use a predicate to check against in the lambda.
3. Using the lambda syntax, you cannot use an enum as the property to discriminate because EF will try to convert the value to primitive type, which throws an exception. If your discriminator is an enum then use the string syntax.

After several iterations and several searches, I realized that the error was the result of using a mapped property as a discriminator. This seemed to be the most common issue others experienced; this usage does not appear to be supported. As I mentioned earlier, we have this working somewhere else. After reviewing that implementation, I noticed that the property being used as a discriminator is not  defined on the super class of the implementing class; instead, it’s defined one more level up. Since it’s not acting as a property and discriminator on the super class, then EF does not seem to care.

Ultimately, I added another property to use only as a discriminator. 