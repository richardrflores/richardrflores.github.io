---
layout: post
title:  "Event Sourcing"
date:   2017-05-08 09:58:00
categories: [Event Sourcing]
tags: 	[Event Sourcing]
comments: true
---
I recently took an interest in CQRS (Command Query Responsibility Segregation) to learn how it's used with DDD (Domain Driven Design) for a project I’m working on. CQRS is an architectural pattern that separates a system into two distinct parts, which either changes the state of the system or reads its current state. While on my CQRS journey, I came across several articles and posts which covered Event Sourcing (ES). This was really interesting to me so went in a little deeper to get a better understanding. Here’s what I know.

ES is an architectural pattern where events describing the state of a domain are persisted. The events are saved to an event log, known as an Event Store, which is an archive of the actions in the system. The event log can be queried to determine the current state of a domain. Events can also be reconstructed to determine the past states. This is the primary advantage of ES. Also systems using event sourcing are capable of higher throughput since changes to entities are persisted asynchronously. This is another advantage of ES. 

A word of caution: There should be a compelling business need to use ES! ES is not suitable for CRUD applications where the business logic is primarily concerned with operating on the current state of the domain. ES is better suited for domains with rich business logic that are fairly complex in nature, such as a temporal domain that is sensitive to history. Ultimately, the need to replay history should be core to the business logic. 
