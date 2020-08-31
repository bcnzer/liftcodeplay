---
author: "Ben Chartrand"
title: "The death of a user"
date: "2020-08-31"
image: "cody-fitzgerald-O0Tr0mrzXLA-unsplash.jpg"
tags: [
  'Life'
]
---

It was 2013 and I got received a call from one of my users.

###### Photo by [Ryan Parker](https://unsplash.com/@cfitz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cessna?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# The news
At the time I was working on a aviation maintenance software system. The conversation went something like this

**Them**: Hey Ben - how do I remove an aircraft [from your software system]?

**Me**: You find the aircraft and change the status to Inactive. Why do you ask? 

**Them**: the plane crashed, total loss

The news hit me like a strong punch to the gut. I was shocked.

# Some background

Your car needs maintenance - scheduled (due every X days), unscheduled (something broke) and recalls. 

Aircraft are the same but they have a lot more of it and it can get complex. The program I wrote managed all this plus a whole lot more. 

The person who got in touch was the Maintenance Controller, responsible for planning and signing off all the work. 

Pilots would enter their flight times (hours, landings, etc) and would occasionally run a report to see what was coming up with maintenance. 

# Why it bothered me

## The first link
The first link I received from my user included a photo of the wreckage. It's seared into my memory. 

I can't find it that article with the image. [This article describes the incident](https://www.standard.net.au/story/1796724/trainee-pilot-dies-in-hamilton-air-crash/) and there's [this one as well](https://www.standard.net.au/story/1796724/trainee-pilot-dies-in-hamilton-air-crash/). 

## I knew the customer well
I'd known this airline for years. They were my first customer. I had gone onsite on several occasions, spent time with many members of the team and even met some of their families. I considered many of the staff my friends.

In this case I didn't know the pilot. They were a trainee at the flight school associated with the airline. 

The aircraft was loaded in the system so he would have entered his flight time into my system. It's likely I would have seen their name. I recall seeing the aircraft registration (rego) on many occasions.

I felt so bad for my friend, the maintenance controller. You could hear the heaviness in his voice as he spoke - the type of tone that immediately gives away that something is wrong.

## Was it my system?
That was one of my first thoughts when I heard the news. My system calculated how long a piece of work had left i.e. if it was due every 365 days or 1,000 landings, it might tell you there was 29 days and 123 landings left. Perhaps it was badly off?

The simple answer was no, that wasn't it and my user wasn't even suggesting that was the case. I later discovered it was a case of pilot error. 

Despite this, this incident would haunt my thoughts whenever I built another feature to this program. It reinforced 
* the importance of quality and the need to push back if I feel quality is being sacrificed for, say, speed
* the need to listen to users
* the importance of digging into stats & errors. Especially at any hint that something could be wrong, such as exceptions from [Raygun](http://www.raygun.com/).

# Leaving it behind
As time went past my team and I would hear news of other incidents and deaths involving customers. None were users of my maintenance system but, once again, the sorrow and grief was felt as we knew the customers well.

Years later I left that company. I had put in 10 years and felt like I was leaving on a high note.

A small, blessed relief was knowing I wouldn't receive another call of a crash.

To this day I periodically force myself to scan the news in the hope of finding nothing as I still care about the people within those airlines. 