---
layout: post
title: "Improving setup of ASP.NET MVC application with RequireJS and KnockoutJS"
date: 2014-12-09 21:56:25
published: true
categories: [MVC, RequireJS, KnockoutJS]
tags: []
---
In this post I want to improve upon the setup I covered in the previous post [Setting up an ASP.NET MVC application with RequireJS and KnockoutJS]({% post_url 2014-09-16-setting-up-requirejs-with-aspnet-mvc %})

I wasn't crazy about having to implement the '@Html.InitPageMainModule()' helper in every view to setup the page module. I do like that it's declarative, but ultimately it's unnecessary.

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

To improve upon this we can take advantage of the HttpContext.Current.Request and VirtualPathUtility to get the path and file name of the view we want to define a module for.

{% highlight c# lineos %}
using System.Text;
using System.Web;
using System.Web.Mvc;

namespace RequireKoPlayground.Extensions
{
    public static class RequireJsHelpers
    {
        public static MvcHtmlString InitPageMainModule(this HtmlHelper helper)
        {
            var require = new StringBuilder();
            var scriptsPath = "~/Scripts/";
            var absolutePath = VirtualPathUtility.ToAbsolute(scriptsPath);
            var modulePath = string.Format("Controllers/{0}", helper.ViewContext.RouteData.Values["controller"]);
            var pathstring = HttpContext.Current.Request.FilePath;
            var pageModule = VirtualPathUtility.GetFileName(pathstring);

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

We no longer have to pass in the name of the view as we were doing previously and can remove the call to the RequireJsHelper in our view and place it in the layout of the web application.

{% highlight html lineos %}
@using RequireKoPlayground.Extensions
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
        @Html.InitPageMainModule()
    </body>
</html>
{% endhighlight %}

So long as you stick to the controller convention for your modules described in the first post, this should just work.

