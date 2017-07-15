---
title: Thinking In Event Streams
layout: post
date: 2017-07-15 13:15:19  
categories: ["Event Sourcing"]
tags: 	["Event Sourcing"]
comments: true
---
In a previous post on [event sourcing](http://richardrflores.com/2017/05/08/event-sourcing.html), I mentioned that there should be a compelling business need to use event sourcing, but how do we know it’s a suitable pattern? After talking to several colleagues I was introduced to the idea of thinking in event streams in order to figure out if event sourcing would be an appropriate given a new new project or adding to an existing solution. Jimmy Bogard commented on [Event Sourcing Revisted](https://lostechies.com/gabrielschenker/2015/05/26/event-sourcing-revisited/) by Gabriel Schekner, in response to this same question. His process is to determine whether the business (not the developers) thinks and models their problem as a series of events, as a way of deciding to use an event sourced system. If we're lucky the business will think about and actually model their system as a series of events. They might recognize events in their domain, but I don’t think it’s likely they would have the technical expertise to model the system as a stream of events without input from technical experts familiar with design of event based systems. So it's up to us, not only be able to recognize events in the domain, but to transform those thoughts into event streams. So how do we get there? We need to start thinking in event streams!

How do we make the switch? The first part is knowing what an event is. I describe the essential characteristics [here](http://richardrflores.com/2017/05/24/essential-characteristics-of-domain-events.html). Essentially and event is an effect resulting from some action. An important thing to recognize is that events describe non-trivial changes that occur in the domain. To illustrate, let’s look at an example from a billing application.

When a charge is submitted to the system, then a charge is created. When a charge is created, then the system codes the charge. When the charge has been coded, then the system creates a journal entry. When a journal entry is created, then the system generates an invoice. When an invoice is created, then the system sends the customer an invoice.

The process above is obviously a simple example, but we can guess that the domain at this point is a billing domain of some sort and we can see the non-trivial changes occurring in the domain as events:

* ChargeCreated
* ChargeCoded
* JournalEntryCreated
* InvoiceGenerated

Now I’ve actually described several events that can occur in the billing domain. However, the key thing to note when identifying events is the tense. Events are always past-tense and each one is potentially an event stream in the system. 

Another, less apparent event stream is a domain change that occurs on an entity in the past. For example, in a billing service, where a new fee must be calculated from a given volume of product and a daily market rate, when the daily rate changed. If we knew the rate to be a given amount on the day the fee was first calculated, but was in-fact supposed to be a different amount and not detected until some time after, then we are dealing with a bi-temporal domain (what actually occurred vs. what was known). So if you have a non-trivial event happening in the context of a bi-temporal domain, then this is probably another potential event stream in the system. In the case the source of the event stream would be the DailyRateChanged event. This change would certainly influence the calculation of the fee, in this case it would result in a new fee being generated all together.

Ok, great. I've shown you how to identify events that can occur in a notional billing system. But what actually makes them event streams? The term 'event stream' comes from event stream processing, which is loosely defined as the process of analyzing a flow of event-based data as it’s created. However, in the context of event sourcing, there is more to it. Not only must the events (the history they represent) be core to the business, but replaying events should also be core to the business. If the history is core to the business, say for regulatory requirements, and if replaying the event streams provides behavior that will influence a change to the domain, such as the case in the second example above, then I would say you have a legitimate event stream, thus a good case for event sourcing, but both elements should be present. Using event sourcing, for purely non-functional reasons (requirements), without buy-in from the business, delegitimizes the event stream and not a good case for using event sourcing in my opinion.

In closing, what I’ve described in this post is "a way" to start thinking in event streams as a way to determine if event sourcing is a suitable pattern. We should be able to identify key events that are important to the business and transform them into event streams. However, the business should ultimately decide if the history of events and the need to replay them is core to the business. If so, then we have a legitimate event stream and a good case for using event sourcing. Once you have determined that event sourcing is an appropriate solution then you can use a more formal way of identifying events, called Event Storming which is a group modeling technique that involves all the key stakeholders, including the business. Checkout Alberto Ziobrando’s introduction to event-based modeling: [Introducing Event Storming](http://ziobrando.blogspot.com/2013/11/introducing-event-storming.html) to learn more.

Anything I might have missed? Any additional insights from interactions with stakeholders? If so, please share in the comments. I’d like to know more.
