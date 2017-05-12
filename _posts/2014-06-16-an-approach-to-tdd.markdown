---
layout: post
title:  "An approach to TDD"
date:   2014-06-16 21:21:56
categories: ["TDD"]
tags: 	["TDD"]
comments: true
---
Over the past few months I've had to coach some junior developers on how to TDD. Although most of the devs I've been working with are knowledgeable about TDD, putting it to practice seems to be a challenge. Most of them know how to write unit tests, but are not actually practicing TDD where the development of tests drives the development of the code. Instead, they are writing the code first and then bolting tests on afterward. Although this approach works, it is not TDD; it's unit testing. And unfortunately, misses the main benefit of TDD which is to write testable code that adheres to SOLID design principles; in particular Interface Segregation and Dependency Injection.

So I get that writing tests for things that you are not familiar with can be challenging. I'm definitely guilty of that! I'll typically hack around for a little bit to explore something I'm not familiar with to try and figure out how I can test it. Ideally, this is probably where you would actually *want* to start writing tests. I know, it's crazy right! Anyway, this is not really the case with the devs I've been working with. There are a few things that seem to be a challenge:
1. Where to start?
2. How to organize the unit test??
3. Use a mock, stub, both!?

**To address these challenges I unusually recommend the following approach:**

**Identify the system under test**  
What are we actually going to test? First off, we only test public methods or behavior that is exposed. If we are practicing proper OOP, then everything else should be hidden. If we consider the Single Responsibility principle, then we should be isolating our test to a single unit or method in the system under test.

**Organize your unit test using AAA**  
Now that we've identified what we are going to test, we organize our unit tests into an "Arrange", "Act", and "Assert" sections using comments. The extra key strokes may be a pain or make you feel like a total noob, but I think the mental tickler this provides is well worth it, besides just properly organizing your unit test to more easily identify its functional components. So starting just under the "Arrange" comment, we typically start with some kind of variable representing the system under test and using productivity tools such as ReSharper or CodeRush we begin developing the unit to test.  

**Decide what kind of test you are going to write**  
What kind of test are we going to write? Will it be a state based test or an interactive based test? State based tests are designed to test outcomes, where as interactive based tests are design to test interactions between collaborators. If you are writing an interactive test, then you will probably be using a mock to assert some kind of behavior between the test method and an external dependency, usually the mock. If you are writing a state based test, then you will probably use a stub. Keep in mind, this is just a guideline. You might need both, depending on the complexity of your system under test. Rhino mocks actually allows you to mix and match mocks and stubs, a reason why it can be so confusing. Another way to look at it is that stubs are inputs to the system under test, where as mocks are inputs to the unit test itself. Roy Osherove wrote a short post explaining the differences between the two and when you should use them, on his blog. [Mocks and Stubs - The difference is in the flow of information](http://osherove.com/blog/2007/9/16/mocks-and-stubs-the-difference-is-in-the-flow-of-information.html).

{% highlight c# linenos %}
// A contrived example of a simple unit test with a mock service

[Test]
public void MyMethodShouldCallDoSomething()
{
	// Arrange
	var sut = new SystemUnderTest();
	var service = MockRepository.GenerateMock<IMyService>(); 

	// Act
     sut.MyMethod()

	// Assert
	service.AssertWasCalled(x => x.DoSomething);
}
{% endhighlight %}  