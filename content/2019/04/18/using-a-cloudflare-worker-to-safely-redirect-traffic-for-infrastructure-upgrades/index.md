---
author: "Ben Chartrand"
title: "Using a Cloudflare Worker to Safely Redirect Traffic for Infrastructure Upgrades"
date: "2019-04-18"
image: "cloudflare-worker-redirect.png"
---

On 10 Jan 2019 I had the privilege of writing a blog post on the Cloudflare blog: [Upgrading Cloud Infrastructure Made Easier and Safer Using Cloudflare Workers and Workers KV](https://blog.cloudflare.com/upgrading-cloud-infrastructure-with-workers-kv)

In the above blog post we were migrating, at [Timely](https://www.gettimely.com/), our Webhooks and using the Worker to migrate on a per-endpoint basis. That worked really well.

We have had a lot of progress and have since moved onto our main web app: an ASP.NET MVC app. This required a bit more effort.

## Problem 0

How would we know the business (specifically, the business ID) if we're going to redirect by it?

We setup a special version of our code on the new infrastructure that would return a cookie with the business ID.

### Problem 1

We chose to migrate by business. Specifically:

- Test businesses go first
- High value businesses go last
- Everyone else gets migrated using a Max Business ID. We slowly raised that value over time.

To accomplish this we created three Worker KV keys. The first two were comma separated lists of IDs and the last was a single value.

### Problem 2

POST operations didn't work. This struck us as odd as we had no problem to date. Upon closer inspection the problem was **redirections**.

In an ASP MVC application a POST operation can be followed by a 301 redirect. For example: you press OK to login. If successful you redirect to the main page our application. Or perhaps you saved a staff member's details. If successful you go to the list of all staff members.

When you redirect a request you have to request a new request object and reinitialize it. Turns out there's a [redirect](https://developer.mozilla.org/en-US/docs/Web/API/Request/redirect) parameter you had to set.

### Problem 3

We had some pages that had full links (not relative links). Thankfully they were only in one page but these links came from the body we generated server-side. To change these we could have deployed a change. Instead we used the Cloudflare Worker to inspect the body and do a find & replace. Thankfully that proved straightforward.

## Result

Once we resolved all of the above we came up with the following. This allowed us to take our existing app and slowly transfer traffic to new infrastructure.

Once we're confident everything is 100% we will change the DNS to permanently point to the new infrastructure and decommission this Worker.

{{< gist bcnzer c1f2008099ff9599d12a915ddec712ed >}}
