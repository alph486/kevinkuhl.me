---
title: "The Startup's Devops Survival Kit"
date: 2022-02-27T20:46:53-06:00
draft: false
image: "/images/kit.jpg"
tags:
    - devops
    - tech  
    - MUX
---

As a Site Reliability Engineer, I feel like I can share a secret with you non-DevOps types: even we hate doing DevOps work. Here’s proof from an ops company, MetricFire:

![MetricFire Screenshot](/images/Metricfire.jpg)

We’re the type of people who constantly complain about keeping the house clean, get a weird joy out of wiping countertops. Then we build robots to do it. Then sometimes the robots smear crap all over the floor. We have good intentions though.

But, if you’re here you don’t want to spend tons of time making robots. You want to make the thing that customers are going to use. Whatever your situation, we’re here to remind you that there are low or no cost / effort tools to get you back to work. It’s also worth mentioning that while Site Reliability Engineers (SREs) are seen as the Paladins of the Dev Ops culture, they are by no means the only people to hold the torch. In fact, knowing about any of these simple tools will make you a **_significantly_** more valuable teammate anywhere you go.

Whether you’re a one-person-show, a leader of a team, on a big team with big trouble, or a small team with…big trouble, we are making this series about DevOps Survival Kits for you. We’re talking about platforms, tools, and practices that are approachable and efficient to solve these problems as a developer without a big SRE team. We’ve added **_cost_** and **_mission_** so you can decide what makes the most sense for your needs.

Today we’re focusing on a kit for developers in small teams, founders of startups, or those who are striking out on their own.

Mix and match these suggestions to build your own kit for your unique situation. There are no rules here! We’ll add some more kits for more use cases in coming weeks.

The StartUp Survival Kit
------------------------

**Budget**: _LoL_  
**Mission**: _Developer Efficiency, Tighten Development Loop, Establish a stable baseline_

You are a one or few man show, pre-launch or at launch and are doing stuff you don’t wanna do, like:

*   Figuring out why it worked locally and not on the server.
    
*   Clearing Disk Space / Managing Memory.
    
*   Debugging arcane database problems.
    

You want:

*   A stable and transparent way to deploy things to production with as little friction as possible.
    
*   Simple, transparent debug info on data layer issues.
    
*   Medium Scalability; you value your time over scale, but not as much as you value being frugal.
    

### Heroku or Digital Ocean (PaaS)

The golden rule of getting started in my mind is: **If you don’t have to host it, don’t**! You may work with people with experience in Infrastructure as a Service (IaaS) platforms like Amazon’s EC2 or Google Cloud Platform. Even managed Kubernetes, like [GKE](https://cloud.google.com/kubernetes-engine) as much as I love it are more maintenance than you need. Try your best to woo them toward a Platform as a Service that you agree on instead. My first choice is [Heroku](https://www.heroku.com/) and that’s what I’ll focus on here. [Digital Ocean](https://digitalocean.com/) is another popular and stable choice. For critics out there I will say, of course, there can be economies of scale in using an IaaS Platform over time, such as using [Reserved Instances](https://aws.amazon.com/ec2/pricing/reserved-instances/) or things like that. But that’s not what we’re after here.

The bottom line is that Heroku is [free or cheap](https://www.heroku.com/dynos), [can scale to enterprise level](https://www.heroku.com/enterprise), and already has the baseline of everything you’re going to need: [**Logs**](https://devcenter.heroku.com/articles/logging)**,** [**Metrics**](https://devcenter.heroku.com/categories/monitoring-metrics)**,** [**Build and Test**](https://devcenter.heroku.com/articles/git)**, and it** [**speaks CLI**](https://devcenter.heroku.com/articles/using-the-cli) **for all of it**. You sign up, install a CLI, commit a `Procfile` that tells them about your app and you’re ready. This is what that file looks like:

`web: java -jar target/myapp-1.1.1.jar`

That will run a process called `web` in a container called a Dyno that has certain resources. You scale it like this:

`$ heroku ps:scale web=2`

Now you’ll have two web containers. Logs is easy too:

`$ heroku logs web —tail`

Look familiar? All the metrics are in the UI. I know this sounds too good / simple to be true. Sure there are limitations at some scale, but who cares right now? If you spent 2 days learning this platform over something you already know that’s more dense, like AWS EC2, you’d already be ahead.

If for some reason you need to use AWS as part of your infrastructure, it’s worth noting that all of Heroku’s infrastructure is colocated in AWS, so you will not have to worry about significant human or latency cost to connect to those resources.

### SQL DBaaS

[There’s not much](https://www.2ndquadrant.com/en/blog/postgresql-is-the-worlds-best-database/) that a battle tested SQL Database like Postgres or MySQL can’t do. That’s why I recommend to jump in full on with a solid SQL flavor, hosted at a reliable company. I have nothing against NoSQL or more novel data storage platforms. Just, in my experience, if you go with SQL, you’ll be able to do everything you want and there is a massive online history and community to help you do it correctly and in a stable way.

If you’re using Heroku or Digital Ocean for your PaaS, they already have [Heroku Managed Data Services](https://www.heroku.com/managed-data-services) and [Managed Databases](https://www.digitalocean.com/products/managed-databases) respectively and you’ll get network proximity and fewer vendors to worry about there.

![](https://images.squarespace-cdn.com/content/v1/61f180454156ae33f1ddba3b/64bdef5e-305c-42cd-9c5a-cb399a8b516e/Untitled.png)

If you needed to extend outside of those for some reason, one can always go back to [Amazon’s RDS](https://aws.amazon.com/rds/?dn=1&loc=3&nc=sn). For all their complexity, Amazon’s RDS databases are reliable and feature rich. They offer backups/restores, custom settings to tweak query performance, and more. Aurora, their own flavor of a few types of SQL databases like Postgres and MySQL, I was skeptical of a few years ago, but did what it said on the tin quite nicely with no surprises except for a nice [performance](https://aws.amazon.com/rds/aurora/faqs/#Performance) boost.

### PaperTrail

If you are locked into something other than a PaaS like the above, you might be stuck managing logfiles on a server. While it seems like a path to spending money or dealing with Yet Another Vendor, I recommend you take a look at [PaperTrail](https://www.papertrail.com/). The obvious benefits are that it's super cheap, won’t let you overspend, and you don’t have to schedule `logrotate` anymore. The second is that they have an, in my opinion, _amazing and familiar_ interface for dealing with logs:

![PaperTrail's Live Tail Feature](https://images.squarespace-cdn.com/content/v1/61f180454156ae33f1ddba3b/06df7d49-c078-4263-ad30-50005296fae6/papertrail-tour-headers_live-tail.png)

Their interface will provide a clutter-free “demux” of your logs into a Live Tail logstream that is delightfully easy to navigate, search, and filter. For most developers I feel like this is a nice step into removing something unfamiliar, while keeping something very familiar. There is even a [CLI tool](https://github.com/papertrail/papertrail-cli) out there to give you all this on your terminal, which makes it even more accessible.

Finally, and as a DevOps advocate this is my favorite, the 1GB / 7$ / mo. limit means that if you start early you will _only log the things that are important_. In my experience, companies I’ve worked with have overspend tens-of-thousands of dollars on metric/monitoring platforms because of legacy logging practices. This helps set a good habit from the start.

### Task Scheduler

Maybe _the_ most common thing for any company or team to use is scheduled jobs. I don’t think I need to go into this very deep, just offer some suggestions on platform. You’re gonna need ‘em. So do it as lean and clean as you can.

Unfortunately, Digital Ocean doesn’t support a built in scheduler. Heroku has one via an add-on called [Heroku Scheduler](https://devcenter.heroku.com/articles/scheduler). You can also natively schedule `worker` type Dynos using the `Procfile` syntax I mentioned in the first section about Heroku, which will expect a process to stand up and spin, like a Daemon. But if those exit, it will try to restart it. Scheduler will just run a process at a scheduled time until it completes.

If you’re not in Heroku, using a Scheduled Lambda in AWS is a very common practice. You might want to keep a repo of all your Lambda code and make sure you keep it updated since there’s not a built in deploy pipeline.

Finally, if all else fails, start up a tiny Linux VM and `crond` is your friend. Here’s a [great reference](https://ostechnix.com/a-beginners-guide-to-cron-jobs/) on Cron. Again, make sure to version all your scripts in a repo somewhere.

These are all the things I usually start with when thinking of what I need for a project. There are tons and tons of resources to solve these problems, but what you don’t need when you’re trying to ship a protoype or a product, is distractions. So these suggestions try to minimize those as much as possible in favor of familiar, inexpensive, and easy to use tools. I hope this has been helpful for those of you that can’t stand being a Server Jockey. We’ll be back soon with some more Survival Kits around Automation, Security, and more.

And if you need help with any of these or anywhere else on your journey, feel free to [email me](mailto:mux@kuhl.mozmail.com).