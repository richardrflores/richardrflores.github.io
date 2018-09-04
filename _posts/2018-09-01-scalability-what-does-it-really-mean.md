---
title: 'Scalability: What does it really mean?'
layout: post
date: '2018-09-01 10:30:00'
categories:
- Design
- Architecture
- SOA
tags:
- Design
- Architecture
- SOA
comments: true
---

The term "scalability" is often included in the list of quality attributes when making key architectural decisions, but what does it really mean? Scalability is a non-functional requirement that refers to the ability of an application to maintain an optimal level of performance as usage increases. Achieving this ability might seem pretty straight forward but depending on the architecture of a particular software scalability can mean different things. 

Typically, when we talk about scaling, we are talking about scaling up, also known as vertical scaling, which is the application of additional resources to a software application, such as cpu, memory, storage and network speed. It's easy to understand how an application can benefit from this type of scaling but, when we are talking about systems, distributed systems to be more specific, then we are usually talking about scaling out, known also as horizontal scaling. This can be done a few ways.

The first way is achieved by design since distributed systems are made up of a collection of abstract services that operate independent of one-another but cooperate in a workflow to complete some desired business functionality. With this type of architecture systems are inherently scalable because the functionality of the system is spread out across its various components. 

![Scale out by architecture]({{ "/content/site/images/scale_by_architecture.png" | prepend: site.baseurl }} "Scale out by architecture"){: .center-image }

Another way systems can be scaled out is by deploying its individual services as nodes, onto multiple servers, in order to balance their use across the servers. This has the added benefit of vertically scaling each server independently. 

![Scale out by nodes]({{ "/content/site/images/scale_by_node.png" | prepend: site.baseurl }} "Scale out by nodes"){: .center-image }

Yet another way is to create specialized components within a service boundary in order to meet quality of service requirements by customer type, such as regular customers versus strategic customers. Or, by product type, such as perishable products versus non-perishable products. The idea here is to have individual components that can be optimized based on a given business scenario.

![Scale out by specialized component]({{ "/content/site/images/scale_by_specialty.png" | prepend: site.baseurl }} "Scale out by specialized component"){: .center-image }

So, when you are tasked with designing an application or system to be scalable you now know what scalability actually means now or at least have some additional information to explore further.

Is there anything I might have missed? Do you have anything to share that would make this post more useful? Please feel free to comment below.