---
title: "How Not to Get Tangled In Stranger-Fig"
date: 2022-03-15T21:03:05-06:00
description: 'How Not To Get Your Team Tangled Up when adopting the strangler fig pattern for replacing an old service.'
image: images/strangler-fig.jpg
draft: false
---

So, you have a relic of a service, and you want to start replacing it with the brand new shiny one. It has containers and async and a fancy framework for persistence object modeling. This is happening in every engineering department I know of, guaranteed. And there is a universal way we approach this problem these days:

1.  Build a Facade to route requests.
    
2.  Introduce a new service to replace one part.
    
3.  Start routing new traffic to the new service while still routing other requests to the old service.
    
4.  Repeat until all parts are replaced.
    

This pattern is well known and well documented. Martin Fowler [wrote about it in 2004](https://martinfowler.com/bliki/StranglerFigApplication.html) after visiting Queensland and observing how Strangler Fig trees subsume existing trees, seeing it as a good metaphor for how we refactor code. Since then, everyone from [Microsoft](https://www.google.com/url?cad=rja&cd=&esrc=s&q=&rct=j&sa=t&source=web&uact=8&url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Farchitecture%2Fpatterns%2Fstrangler-fig&usg=AOvVaw30SIjs5IbN8DD1fhza7AQX&ved=2ahUKEwif9bLN8cj2AhW-mmoFHeZsBvMQFnoECAgQAQ) to [Shopify Engineering](https://www.google.com/url?cad=rja&cd=&esrc=s&q=&rct=j&sa=t&source=web&uact=8&url=https%3A%2F%2Fshopify.engineering%2Frefactoring-legacy-code-strangler-fig-pattern&usg=AOvVaw0U-R9cQD0Q2H5sAAVAxZON&ved=2ahUKEwjB6Lfa8cj2AhW-m2oFHat3DroQFnoECAcQAQ) has written about it as a tried and true method to do what we software people love doing more than anything: replacing garbage with new shiny things.

In the age of Microservices and powerful API Management, it's much easier to implement this than it was in 2004. Due to the popularity of service-oriented architectures, containers, serverless, and event-driven architectures, much of the infrastructure that backs services today make it hard not to use this pattern when upgrading _anything_. 

No matter how easy the frameworks and implementation patterns make it, there are still numerous non-technical potholes ahead of every team that attempts to "Strangle" an old application that, if not heeded, can at best damage credibility and at worst, leave you with two bad systems to maintain instead of one. Here I want to highlight a few of those things to consider so you don't end up hanging yourself with the Strangler pattern (excuse the dark pun).

### Appoint a Facade Tech Lead

If there's one component this strategy will not work without, it's a Facade in front of the service layers. You need something, existing or new, to sit in front of your service(s) and route requests/data. As with anything, it doesn't matter how good the downstream code is; if the machinery upstream fails, it ain't working. In modern applications, this layer may be used by other teams or people (Ingress in Kubernetes, or a shared NGINX proxy, for example).

As such, teams should make sure at least one person is accountable for the Facade layer. That person can be the gatekeeper for the routing rules and their deployment, coordinating with other teams or users of the layer, and any monitoring you need upstream. Why one person and not just have the team manage it? You certainly can if that will work for you. But in some cases, especially if there are other stakeholders in the Facade, you’ll end up with cases of “I told John last week we were updating the HTTP Headers…” when John is on vacation. It’s often best to have that one point of contact to handle each release and change to the contract reliably.

If your Facade will be new, there’s the consideration of building and maintenance. Too many cooks configuring this could result in inconsistencies with your contract or request lifecycle in the case of HTTP APIs. You can also get sucked into a vendor or technology debate. Best to select someone to make that decision and move on. If you can, lean on existing proven technologies like NGINX, Elastic Load Balancing, or an API Management Solution if need be.

### Feature Flag Your Glue Code

You will likely end up with some version of code whose job is to conform with an old standard, like transforming data into an old format or making calls to an external service to get information before filling the request. You and your team tell yourself that these are temporary and will eventually disappear. \***smirk**\*

If you can do so without too much overhead, go ahead and make that code temporary by using a feature flag to indicate that it should have the “old” behavior or the “new” behavior and make both implementations living, testable code. This will signal to even new developers that you support old _and_ new functionality and that one should go away. Furthermore, it forces you to implement the new way and provides an easy way to refactor the old way.

You don’t need a “Feature Flagging Platform” to do this. When I worked at Wayblazer we used our Proxy to add a header to each request to old software with a version flag after handling the inbound request. Then when the project was complete, we removed that logic—no change to the contract, no headache.

### Update Your Processes & Checklists

Remember that you may regularly change network/routing configuration and legacy software behavior. You will want to be able to **monitor everything and turn on a dime**. If, for some reason, your team isn't used to self-managing quick rollbacks, smoke/regression testing, or other Site Reliability style work, you're going to need to update your expectations.

Why? Even if everything is going to plan and the new service is _objectively better, faster, and more robust_, no one will care when you can't identify and hotfix a production issue quickly. You'll quickly lose credibility with the business, and it could impact the success of your project. A little alignment on monitoring performance and reacting to an emergency will pay dividends. There _will_ be problems.

### Deprecate Your Old Contract

If you’re working on an API or something with a request contract, chances are your new services will have a new API Contract for clients, or at least would be better served by a change. Once you’ve reached the end of the project and all of the backend functionality is nearly updated, I guarantee you you’ll get senioritis and be ready to put this project to bed.

Hang in there! Don’t declare victory until you’ve announced to clients officially that you will be deprecating old contracts. Make sure someone is on the hook for following through with that deprecation date and that documentation is updated.

Doing this separates people who make long-term sustainable architectures from those who just make another mess to get Strangled in the near term. Finish the job, and clean up the contract, so it’s better for your clients. Make sure your proxies and backend code leverage the new contract’s features.

### Measure and Advertise Your Success

So you’re replacing all that cruft and things are moving/performing well?

Make sure you share that relief with the business! 

There’s no doubt there’s a reason the team is doing this work. Are you saving money? Are you improving the quality of service for customers? Is it faster? Are there new features? Are you able to deliver quicker? Make sure to find the crucial metrics and share them when you hit milestones!

Broadcasting these is going to buy you credibility. People outside the team love to hear these things; they’re not just pandering. It gives salespeople something to brag about to customers, HR people to feel good about whom they helped bring on board, high-level leadership that the company is moving forward and staying modern, and engineering leadership something to talk about whenever they do whatever the hell they do **😉.**

### Parting Words

Got any additional advice? Please share with me on [LinkedIn](https://www.linkedin.com/in/kkuhl/) or via any of the links below. Here's one final word from Fowler on the subject:

> There's another important idea here - when designing a new application you should design it in such a way as to make it easier for it to be strangled in the future. Let's face it, all we are doing is writing tomorrow's legacy software today. By making it easy to add a strangler fig in the future, you are enabling the graceful fading away of today's work.

Do it for your children 😊.