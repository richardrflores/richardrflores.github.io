---
layout: post
title:  "Coercion of types (soft casting)"
date:   2015-03-18 14:30:00
categories: ["C#"]
tags: 	["C#"]
---
Today a colleague of mine used the word "coercion" when referring to a soft cast in code. Some thoughts I captured about this.

Hard cast vs soft cast or coercion vs. explicit casting. In C# this is the difference between using:

{% highlight c# linenos %}
// Hard cast    
var casted = (SomeType)castMe;

// Soft cast
var softCast = castMe as SomeType;
{% endhighlight %}