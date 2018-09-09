---
layout: post
title: CI/CD with Azure Storage Static Sites
categories: [DevOps]
fullview: false
comments: true
permalink: /2018/09/azure-static-sites-cleanup/
---

[Static web hosting](https://azure.microsoft.com/en-us/blog/azure-storage-static-web-hosting-public-preview/) is now a thing in Azure Storage and is pretty easy to setup. There's a small caveat with the CI/CD solution they've provided though.

## Getting setup

I'm not going to go into it here because there a perfectly fine tutorial on [how to setup the static site initially](https://code.visualstudio.com/tutorials/static-website/getting-started) and also how to take that site and [enable a CICD build around it](https://code.visualstudio.com/tutorials/static-website/deploy-VSTS). 

This is what I followed and I recommend the same.

# The Gotcha

In the CICD process suggested, they use an Upload Batch command. And this does exactly what you expect it to, uploads all of the files. So what could be wrong with that?

Well it's subtle. Each time you run the build, it's going to bundle everything up into a neat little JS file and change a little hash number so that if someone goes to your new site, it will pull down the new JS file and avoid caching.

What the default CI/CD process is doing is ignoring that clean up.

![static-site-no-cleanup]({{ site.url }}/assets/media/static-site-no-cleanup.jpg)

The HTML was uploaded to use the right one, so the site will work exactly as you expect and be properly updated. The downside is now you are filling up your storage with useless JS files. This is worse if you are including the mapping files as those are much larger.

# Cleaning up

Regards to how I went about it, your mileage may vary. In my case, I was prefectly content to clear out the container before putting the new static site up. I'm not concerned with the type of site I'm making that theoretically someone might get a 404 for an extremely small moment in time.

If that sounds like your scenario, then the following task might work for you:

![static-site-no-cleanup]({{ site.url }}/assets/media/static-site-cleanup-task.png)

Put this task between the NPM build and the Upload site tasks in the VSTS build process and it'll clean out the storage and you'll only end up uploading your nice and fresh files.
