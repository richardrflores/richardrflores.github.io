---
title: Essential Characteristics Of Domain Events
layout: post
date:   2017-05-24 09:24:00
categories: [DDD]
tags: 	[DDD]
comments: true
---
In my post on [event sourcing](http://richardrflores.com/2017/05/08/event-sourcing.html), I briefly described event sourcing and touched on appropriate usages. I’m planning on writing some more about usages, but for now I wanted to share my notes on the fundamental thing we are dealing with when using event sourcing—Events.

The essential characteristics of events are:
* They occur in the past are typically emitted from an aggregate in response to an action. In the case of a ShoppingCart, “ItemAdded”, “ItemRemoved”.
* They are immutable. Since domain events are a record of a change occurring in the past, they cannot be changed or undone. Subsequent events can alter or reverse the state of the domain. Such as “OrderModified”, or “OrderCanceled”.  
* Events are one way. Events can only be emitted (published) from a single source. There can be multiple subscribers to an event.
* Events should contain information about the event, such as a key identifying the source of the event, and any additional information relative to the domain. 
* Events should describe the intent of the business beyond the name of the event. For example, “Order 1000 was created by customer jsmith”.