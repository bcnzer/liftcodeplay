---
author: "Ben Chartrand"
title: "Fix for Azure Web Apps, Redis and Poor Performance - Min Thread Pools"
date: "2019-09-03"
image: "awa-before-and-after.png"
tags: [
  'Azure'
]
---

I'm hoping the following can help the next poor soul that experiences this problem.

### Background

At work we've been migrating our web applications from Cloud App Services to [Azure Web Apps](https://azure.microsoft.com/en-us/services/app-service/web/). The former is effectively (but not officially) deprecated.

Shortly after we went live with our main app we noticed very poor performance. Average response times were easily +50% slower than before.

![](https://liftcodeplay.files.wordpress.com/2019/09/api.png?w=465)

This struck as really odd as:

- we made only the absolute bare-minimum code changes
- the service tier was very similar to what we had before
- we still had plenty of CPU and RAM available

Turns out the other applications we migrated beforehand had similar performance issues. We just hadn't noticed.

The key difference, according to New Relic, was that time spent with Redis. Previously it was a small sliver of overall response time. Now it was a significant chunk.

Why was this new environment worse?

### Was it the Redis Client?

All our applications are written in C# using ASP.NET. The Redis client is the latest version of the [StackExchange Redis client](https://github.com/StackExchange/StackExchange.Redis).

Over time we realized the StackExchange Redis client is thread hungry. We removed another thread-heavy package but we were still no closer to a resolution.

A colleague tested our app in depth. In the end he felt there was an issue with thread management. A quick google [found this article](https://www.tarunpabbi.com/?p=40) so we decided to give it a shot.

### It works!!!

The fix was as simple as telling the app what the minimum thread pool is for worker and IO threads. We added the following to our `global.asax.cs`.

```
ThreadPool.SetMinThreads(50, 50); // worker and IO threads
```

The difference was immediately apparent, as seen below. Worth pointing out:

- the huge drop in response time
- time spent in Redis is much lower; it's **10x faster**!

![](https://liftcodeplay.files.wordpress.com/2019/09/awa-before-and-after.png?w=1024)

Before and after the min thread change

### Why did this work?

Azure Web Apps has thread limitation functionality. If you get past a certain minimum amount of threads, logic will kick in to determine if/how you get more threads. Unfortunately it's slow to execute. I read somewhere about how it can take up to 500 ms.

That line of code simply states that you get up to X number of threads without this logic applying.

An easy way to determine the minimum threads number is to look at the metrics in your Azure Web App metrics - specifically the Thread Count. Ours said we had 60 on average so 50 was a good starting point.
