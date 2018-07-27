---
layout: post
title: Configuring Swagger to handle MediatR Commands and Queries
categories: [dotnetcore]
fullview: false
comments: true
permalink: /2017/03/configuring-swagger-to-handle-mediatr-commands-and-queries/
---
If you are following Jimmy Bogard's style of MediatR commands and queries that he did within his Contoso University example you'll find that Swagger does not play nice by default with that type of class nesting.

Snippet Example:
<script src="https://gist.github.com/dvanherten/d202b1633561639a40ff3d46dc4b799c.js"></script>

Thankfully there is a solution!

Lets say I have two commands. One called *DeleteAllTheThings.cs* and another called *DeleteAllTheOtherThings.cs* and both have a nested class called *Command*. By default Swagger will start to complain with an error about duplicate schema ids because it is only looking at the Command class of which there are now two. We need something a little more robust than that in how we generate a name. Thankfully when registering Swagger in the Startup.cs we can add an option to customize how Schema Ids get generated.

<script src="https://gist.github.com/dvanherten/fed5f9b0a2a474c05c3417f5bb4f2a71.js"></script>

This bit of code is simply using the full name of the type instead and gluing the wrapping class type name with the nested Command or Query. The result is you get a nicely named class in your documentation too!

![swagger-mediatr3-modeling]({{ site.url }}/assets/media/swagger-mediatr3-modeling.png)