---
layout: post
title:  "Setting up an ASP.NET MVC application with RequireJS and KnockoutJS"
date:   2014-09-16 22:19:24
categories: [MVC, RequireJS, KnockoutJS]
tags: 	[MVC, RequireJS, KnockoutJS]
---
I'm currently working on an ASP.NET MVC project that uses RequireJS and KnockoutJS. If you don't know, RequireJS is an open source javascript library for loading javascript files and modules. RequireJS allows us to encapsulate code into a useful units (modules) and manage the dependencies to other units of javascript code. KnockoutJS is an open source javascript library for creating dynamic UIs. KnockoutJS uses the Model-View-View Model (MVVM) pattern to provide binding and dependency tracking between client side data models and the UI. 

Over the last month and a half, we've been working on some dense javascript code that makes extensive use of RequireJS to manage dependencies. The team that initially implemented the code leveraged some design patterns we are used to seeing on the server, such as abstracting common behavior to a base class (a base view model), using the template-method pattern for preparing data and making Api calls and encapsulating common UI components such as 'Save' and 'Cancel' for reuse. In a large enterprise application, you can imagine how seemingly complicated this code can get and more often than not, we would often run into trouble with data or some dependency not being available. Admittedly, when I was introduced to RequireJS, it was like, "Here's how define a module, reference dependencies, now GO!". I hit the ground running and never really understood how it was setup in the project. So to get a better understanding of what is going and how RequireJS and KnockoutJS are wired up with MVC I created a naive example. You can find the example project on github at [richardrflores/RequireKoPlayground](https://github.com/richardrflores/RequireKoPlayground).

Given that you've created a new MVC web application, install RequireJS and KnockoutJS using the Package Manager Console.

![Install RequireJS and KnockoutJS]({{ "/content/site/images/setup_Mvc_Requirejs.png" | prepend: site.baseurl }} "Install RequireJS and KnockoutJS")

The first thing we will do is add a script tag to the site layout file to setup a baseUrl to load our scripts. There are a couple of ways you can do [this](http://requirejs.org/docs/api.html#jsfiles). For this example we'll create a reference to require.js using the src attribute of the script tag.

{% highlight html linenos %}
<html>
    <head>
        <meta charset="utf-8"/>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Require KO Playground</title>
    </head>
    <body>
        <section id="main">
            @RenderBody()
        </section>
        <script src="~/Scripts/lib/require.js"></script>
        @RenderSection("scripts", required: false)
    </body>
</html>
{% endhighlight %}

Next we'll set the baseUrl manually using a [RequireJS config](http://requirejs.org/docs/api.html#config). The file should be created in the root directory of your scripts folder. You can name the file whatever you want. For this example I named it 'main.js'.

{% highlight javascript linenos %}
require.config({
    baseUrl: '/Scripts/app',
    paths: {
        // the left side is the module ID,
        // the right side is the path to
        // the file, relative to baseUrl.
        // Also, the path should NOT include
        // the '.js' file extension. 
        jquery: '../lib/jquery-2.1.1',
        knockout: '../lib/knockout-3.2.0'
    }
});
{% endhighlight %}

For this example the baseUrl points to the app folder in the scripts directory. I've moved the files we installed int the first step into a folder called lib.

Something worth mentioning is the arrangement of your script files. The Require API documentation says:

> Avoid deep folder hierarchies for scripts, and instead either keep all the scripts in baseUrl, or if you want, separate your interlibrary-supplied code from your app code  

The API docs provide a sample directory layout. Below is the one used for this sample based on the API docs. Note the presence of the app directory which resembles the directory structure of the MVC Controllers folder. More about that later.

![Scripts directory layout]({{ "/content/site/images/scripts_Layout.png" | prepend: site.baseurl }} "Scripts directory layout")

Great! We've got a reference to RequireJS and we've configured it. Now what? Well we need to define a web module. More specifically, we want a an asynchronous module definition ([AMD](http://requirejs.org/docs/whyamd.html)) which is a specialized function wrapped format that require provides. 

For our example we'll add a Default controller and a corresponding view.

{% highlight c# lineos %}
using System.Web.Mvc;

namespace RequireKoPlayground.Controllers
{
    public class DefaultController : Controller
    {
        // GET: Default
        public ActionResult Index()
        {
            ViewBag.Greeting = "Welcome to my Require KO Playground!";

            return View();
        }
    }
}
{% endhighlight %}

{% highlight html lineos %}
@using RequireKoPlayground.Extensions
@{
    ViewBag.Title = "Index";
}

<h2>@ViewBag.Greeting</h2>
{% endhighlight %}

Now we want to do something interesting. For our example we want to create a very basic view that binds some client-side data from a view model using [KnockoutJS](http://knockoutjs.com/) (a dependency we need to load - this is the problem that require solves for us). So in the Scripts/app/Controllers/Default/ViewModels directory we'll define a module that represents our viewModel. This viewModel will provide data for our view and encapsulate data related behavior of our view. The viewModel itself is defined as an unnamed module using RequireJS conventions. 

Some thoughts on AMDs: People often think of AMDs as a way of injecting dependencies into our views and viewModels, which sometimes leads to the thought that this is a way of doing dependency injection on the client. NO! Definitely not that! I like to think of the AMDs as a way of defining classes in javascript. The input parameters to the define function can then be thought of as using directive in C#, allowing the use of the declared dependencies.

{% highlight javascript lineos %}
define(['knockout'], function (ko) {

    var viewModel = function () {
        var self = this;

        self.name = ko.observable();
    };

    return viewModel;
});
{% endhighlight %}

We also need to define a module that represents our view. This module will allow us to separate our UI code, specifically code we would use to manipulate the DOM. This is also where will bind our viewModel to our view using the ko.applyBindings method.

{% highlight javascript lineos %}
define(['jquery', 'knockout', './ViewModels/defaultViewModel'], function($, ko, defaultViewModel) {
    var viewModel = new defaultViewModel();

    ko.applyBindings(viewModel);
});
{% endhighlight %}

So we've defined some modules that will bind data to our MVC view using KnockoutJS. Now we need to add some elements to our cshtml file to bind to. We also need to make a call to require to load the configuration we created earlier and execute our modules. We'll do this using an Html helper.

{% highlight html lineos %}
@using RequireKoPlayground.Extensions
@{
    ViewBag.Title = "Index";
}

<h2>@ViewBag.Greeting</h2>
<p>
    Enter your name? <input data-bind="textInput: name" />
</p>
<p>
    Your name is:
    <span data-bind="text: name"></span>
</p>

@section scripts
{
    @Html.InitPageMainModule("index")
}
{% endhighlight %}

Why do we need an Html helper? Honestly, we really don't need it. We could just add the necessary require statement to the chtml file directly using a script tag, but as you can imagine, this will eventually lead to code duplication. Using a helper we can reduce the amount of code necessary to load our configuration and execute our modules. We also don't have to worry about relative paths. 

The helper below is loosely based off an example by Joe Wilson, from [Volare Systems](http://volaresystems.com/blog/post/2014/05/27/Adding-RequireJS-to-an-ASPNET-MVC-project)

{% highlight c# lineos %}
using System.Text;
using System.Web;
using System.Web.Mvc;

namespace RequireKoPlayground.Extensions
{
    public static class RequireJsHelpers
    {
        public static MvcHtmlString InitPageMainModule(this HtmlHelper helper, string pageModule)
        {
            var require = new StringBuilder();
            var scriptsPath = "~/Scripts/";
            var absolutePath = VirtualPathUtility.ToAbsolute(scriptsPath);
            var modulePath = string.Format("Controllers/{0}", helper.ViewContext.RouteData.Values["controller"]);

            require.AppendLine("<script>");
            require.AppendFormat("require(['{0}main.js'], ", absolutePath);
            require.AppendFormat("function() { require(['{0}/{1}', 'domReady']); }", modulePath, pageModule);
            require.AppendLine(");");
            require.AppendLine("</script>");

            return new MvcHtmlString(require.ToString());
        }
    }
}
{% endhighlight %}

That's it. We should be able to run our application and enter a name and watch that name get generated on the screen dynamically. There is one thing though. I don't really like that we have to use this helper on every MVC view. I think there is a way we can improve upon this in my next post.