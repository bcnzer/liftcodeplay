---
author: "Ben Chartrand"
title: "The app (and manual) that stared my career"
date: "2020-10-25"
image: "weatherstation_large.jpg"
tags: [
  'Career'
]
---

Over 20 years ago I brought a manual to a job interview. This ultimately helped me get my first job.

# Weather Station project

I was in my final year of studying electronics at college. In that last year everyone needed to build a project, which was assessed at the end of the year. 

## Hardware

I chose to build a PC Weather Station. It started with a kit that provided
* PC card with a parallel port
* Wind direction housing and circuit board
* Wind speed housing and circuit board
* Central circuit board, which all the sensors connected to

In addition I build - by hand - a circuit board that measured temperature and light levels. This too sent data to the same central circuit board.

Amongst the more challenging pieces of work was the wiring. I had 37 wires running from the central circuit board. On the other end was a 37 pin connector (parallel connector) which would be used to connect to the PC card.

## Software

The software has two core components:
1) The code which interfaced with the hardware, to get data
2) The user interface

C++ was used as it allowed low-level communication with the hardware and it happened to be the language we were learning at the time.

The tech stack consisted of:
* Visual C++
* MFC 
* Third-party Active X controls for the various dials and guages 
* The IDE was Visual Studio 6

![Weather station software](images/weatherstationsw_large.jpg)

I loved working on the software. I added a whole bunch of features such as logging to a .CSV, creation of a "demo mode" and even an animation in the top left corner with clouds moving past. 

It was about this time that knew then I wanted to work as a software developer once I graduated.

## Manual and Presentation

As part of the project I had to write a manual and create a presentation for my class and teachers. I put my heart into it and received an excellent grade.

# The job hunt

I applied for a job at a local software company. It was an entry level job building installation programs, patches and services packs. 

I showed the interview panel my manual. They were impressed and asked if they could show it to the VP of Software Engineering.

I had a second interview with the VP of Software Engineering and was offered a position as a junior software developer.

I actually chickened out. I didn't feel I could do it. I accepted the junior position and a few months later I grew bored. My manager saw this offered to speak with the VP of Software Engineer. She did, got my second chance at the role and this time I took it!

# Years later

I made an effort to archive what I had left of my project. I made a copy of:
* the software. Turns out I had even made an installation program
* the surviving PDF of the presentation
* some pictures I could find of the hardware

I no longer had the source code, manual or hardware.

## Many, many years later

It's Oct 2020 and I saw my archived project in my OneDrive folder. I decided to download the program and try running it. I'm using the latest Windows 10. I didn't hold out much hope as I wrote it about 22 years ago.

Much to my surprise: **it worked**!

I got bombarded with messages about the trial period being expired on those ActiveX controls but overall it worked. It ran in demo mode, meaning all the controls constantly went up and down.

And now I've written this blog post. It's another form of archiving - another reminder - of this project.