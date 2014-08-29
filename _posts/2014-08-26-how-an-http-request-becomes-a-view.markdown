---
layout: post
title:  "How an HTTP Request Becomes an MVC View"
date: 2014-08-26 21:03:00  
categories: [ASP.NET, MVC]
tags: 	[ASP.NET, MVC]
---
How does a request for a URL become HTML in an ASP.NET MVC application? This information can be easily found by doing a basic search for the ASP.NET http request cycle. This process is pretty well documented, on both [MSDN](http://msdn.microsoft.com/en-us/library/dd381612(v=vs.100).aspx) and the [ASP.NET website](http://www.asp.net/mvc/tutorials/mvc-5/lifecycle-of-an-aspnet-mvc-5-application), so I recommend you go there for a more detailed explanation of the process. So the purpose of this post is for me to articulate how an HTTP Request becomes an MVC View while highlighting the interesting parts.

When I first started programming ASP.NET applications a while back, I remember how important it was to understand the Page Life Cycle so that you could properly write code to initialize controls, control behavior and manage view-state, among other things. It described a series of stages and events in an ASP.NET application in order to convert an HTTP Request to an HTTP Response.

Today, ASP.NET Web Forms still uses the Page Life Cycle concept, albeit its been updated and many new features have been added along the way, such as data binding events. ASP.NET MVC has a similar concept known as the Application Execution Process where an HTTP request becomes an MVC View or an HTTP Response.

An HTTP Request becomes a view by going thru a series of stages and events in the ASP.NET application life cycle.

This process includes the following stages:

* Application Request
* Routing
* MVC Handler Creation
* Controller Creation
* Authentication and Authorization
* Model Binding
* Action Invocation
* Result Execution

The most interesting parts of this process are the Routing, Controller Creation, Action Invocation, and the Result Execution. Model Binding is also very interesting, but probably a more advanced topic that is not necessary to understand (relatively speaking) how a request becomes a view.

Routing. When an MVC application starts the application stores a collection of pre-defined URL patterns, known as Routes, in the RouteCollection of the RouteTable object. The Routes are used to construct URLs dynamically. When an HTTP request is made the UrlRoutingModule finds the first route that matches the format of the URL request to create a RouteData object, which it then uses to create a RequestContext object.

{% highlight c# linenos %}
// An example of a URL request
http://server/application/Products/show/beverages

// A pre-defined URL pattern or route definition
{controller}/{action}/{id}

// The matching URL pattern
/Products/show/beverages
{% endhighlight %}  

Controller Creation. The RequestContext from the Routing stage is used to create an instance of an MvcHandler, which is then used to create an instance of a controller matching the URL pattern of the HTTP request.

{% highlight c# linenos %}
// Copyright (c) Microsoft Open Technologies, Inc. All rights reserved. 
// See License.txt in the project root for license information.

private void ProcessRequestInit(HttpContextBase httpContext, out IController controller, out IControllerFactory factory)
{
    // If request validation has already been enabled, make it lazy. This allows attributes like [HttpPost] (which looks
    // at Request.Form) to work correctly without triggering full validation.
    // Tolerate null HttpContext for testing.
    HttpContext currentContext = HttpContext.Current;
    if (currentContext != null)
    {
        bool? isRequestValidationEnabled = ValidationUtility.IsValidationEnabled(currentContext);
        if (isRequestValidationEnabled == true)
        {
            ValidationUtility.EnableDynamicValidation(currentContext);
        }
    }

    AddVersionHeader(httpContext);
    RemoveOptionalRoutingParameters();

    // Get the controller type
    string controllerName = RequestContext.RouteData.GetRequiredString("controller");

    // Instantiate the controller and call Execute
    factory = ControllerBuilder.GetControllerFactory();
    controller = factory.CreateController(RequestContext, controllerName);
    if (controller == null)
    {
        throw new InvalidOperationException(
            String.Format(
                CultureInfo.CurrentCulture,
                MvcResources.ControllerBuilder_FactoryReturnedNull,
                factory.GetType(),
                controllerName));
    }
}
{% endhighlight %} 

Action Invocation. Once the controller matching the HTTP request is created, the ControllerActionInvoker uses the ControllerContext, which has information about the HTTP request, to determine the action method of the controller class to call, and then calls that method. 

{% highlight c# lineos %}
// Copyright (c) Microsoft Open Technologies, Inc. All rights reserved. 
// See License.txt in the project root for license information.

protected override void ExecuteCore()
{
    // If code in this method needs to be updated, please also check the BeginExecuteCore() and
    // EndExecuteCore() methods of AsyncController to see if that code also must be updated.

    PossiblyLoadTempData();
    try
    {
        string actionName = GetActionName(RouteData);
        if (!ActionInvoker.InvokeAction(ControllerContext, actionName))
        {
            HandleUnknownAction(actionName);
        }
    }
    finally
    {
        PossiblySaveTempData();
    }
}
{% endhighlight %} %} 

Result Execution. The action method receives user input, prepares the appropriate response data, and then executes the result by returning a result type such as ViewResult, which renders a view and is the most-often used result type. A view contains the HTML markup and content that is sent to the browser. 

{% highlight c# lineos  %}
// Example of About Action from the MVC new project template
// Note: ViewResult is derived from ActionResult
public ActionResult About()
{
    ViewData["Title"] = "About Page";

    return View();
}

/* The view that is rendered. */

    ViewBag.Title = "About";
}
<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>

<p>Use this area to provide additional information.</p>
{% endhighlight %}