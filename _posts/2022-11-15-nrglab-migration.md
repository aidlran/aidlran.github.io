---
layout: post
title: Migrating NRGLAB.uk to Hugo
author: Aidan Loughran
date: 2022-11-15 15:00:00
categories: web-dev
slug: nrglab-website-migration
image: /img/nrglab.uk-2022.jpg
---

Earlier this month I migrated the website of my events and music brand, [NRGLAB](https://nrglab.uk), from a dynamic [Express app](https://expressjs.com) to a fully static [Hugo](https://gohugo.io) website.

I also set up a GitHub Organisation to move the repo from BitBucket. The code can be found [here](https://github.com/nrglabuk/website).

[![NRGLAB homepage](/img/nrglab.uk-2022.jpg)](https://nrglab.uk)

## Background

**Version 1** of the site, from around 2020-2021, was made during my 'intro to web dev' days, and so it was PHP and MySQL based and hosted on a VPS. 🤢

**Version 2** of the site came in December 2021, when we migrated to Express.js (a Node.js framework). Hosting was also moved to AWS.

Now comes **version 3** in November 2022 and with some web development experience under my belt, I believe that a static website framework is best for this project at this point as the benefits vastly outweigh any drawbacks for a website of this size and type. With my AWS free tier expiring in December and with some experience using GitHub pages for free hosting, it was finally time to take the plunge.

## The Differences

Before, we were using server-side rendering to generate web pages at request time with data from a live relational database.

Now, with Hugo, everything lives inside the project. We use YAML and Markdown files in place of a database. YAML is used for data, which in our case is things like events, artists and music. Markdown, on the other hand, is used for content, which could be things like blog posts. Hugo then uses this data along with templates to pre-render the entire website at build time.

Let's take a look at some of the benefits and limitations of this approach.

### Benefits

#### Speed!

Everything about the new system is fast. Hugo is written in Go, which is *blazingly* fast. The entire website now builds faster than it would take to initialise the old Express app.

The website itself is as fast as it can possibly be too because all of the files that a user can request have already been fully rendered and put through a minification process. There is no server-side processing after the fact, so the only limiting factors here are the speed of your web server and hosting throughput.

I also had to set up a GitHub Workflow to deploy the website to GitHub Pages whenever the main branch is pushed to, so now everything is automated! This would have taken a lot more effort to implement while self-hosting.

#### Free hosting!

Static websites can easily be deployed to and hosted with GitHub Pages completely for free!* You can use custom domains and SSL too. This was a big factor driving me away from continuing to use AWS for the project in its current form. Really, our only web costs now are for maintaining the domain registration.

<small><i>* If the repo is hosted publicly on GitHub.</i></small>

Traditionally GitHub Pages sites have used [Jekyll](https://jekyllrb.com), a framework written in Ruby (in fact, the framework that my personal website uses at the time of writing, although I'm likely to migrate that at some point too.) However pretty much anything can be deployed there by writing a GitHub Workflow.

#### Developer friendly!

The project is far more approachable now. Markdown and YAML are very human friendly, so they can be written from the GitHub web interface with no problem! This saves us time and money as we don't need to invest into setting up a CMS for the less techie types.

#### Tiny tech stack!

Hugo comes as a lone executable file. It's available cross-platform and you don't even need to install any dependencies. Just download the binary for your system and add it to PATH (if you want to) and you're all set!

Now all we have to worry about now is the Hugo project, GitHub and CloudFlare. This is a huge improvement from before where we had a Node project with several dependencies, not to mention managing AWS, a Linux server, and a DBMS!

This is also great from a security standpoint too as reducing the size of your tech stack also reduces your attack surface.

### Drawbacks

#### Frequent rebuilds

Whenever you make any changes to the site, no matter how small, it will need to be rebuilt. Fortunately Hugo builds incredibly quickly and has a built in server with file watching and auto-reload capabilities, so you can develop without even noticing this.

#### Limitations

Most limitations of static site generators are that you can't do some of the more complicated web stuff that you may be required to, however this can always be overcome by adopting a **hybrid approach**  and using static generation alongside other architecture (see the [conclusion](#conclusion)).

##### Lack of POST requests

All Hugo does is output static files that are served to our users when their browser sends a relevant GET request. It's up to GitHub what web server they decide to use to serve them. We have no control over the backend, so we cannot process form data unless we either use a third party or go back to setting up our own web server and API again.

We've retired our 'contact us' page for now. It wasn't really used for much besides spam (which admittedly was rather amusing to look through from time to time.) We prefer people contact us directly over email or messenger anyway.

##### No CMS or admin panel

Granted, we didn't exactly have one before. We didn't have a lot of content and data, so we'd just write web pages and edit the database manually. Now, though, we're using Markdown and YAML, so it's easy to add or edit content to the website once the templates and build process are all set up.

It would absolutely be possible to set up a CMS to work with Hugo, but it would involve time and money. Besides, GitHub's web interface really works great for our purposes!

## Conclusion

Of course there are more options when it comes to web architecture than just the two discussed here. There is also client-side rendering using JavaScript frameworks, which are great for more complicated web and mobile apps. Some even use advanced hybrid techniques like hydration.

I just think that anything like that is overkill for a small business website and leads to a worse developer and user experience. Too much of the web relies on bloated JavaScript frameworks where they really don't belong. Static generation is awesome, fast, overlooked, and should be used where possible. Besides, you can take a hybrid approach and use static generation alongside your other web services. You could also have a simple REST API in a framework or language of your choice to handle form data sent via POST request, for instance.

I guess I just prefer minimalism. I'm hyper-allergic to dependencies and bloat. I like microservice architecture over monolithic, I like using the right tool for the job, and I like to do as much in-house as is sane to do. I've come to believe that a lot of the tech stacks and frameworks people are using in their projects are completely overkill, but perhaps that's a conversation for another day.
