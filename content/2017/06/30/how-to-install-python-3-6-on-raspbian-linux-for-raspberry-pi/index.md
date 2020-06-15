---
author: "Ben Chartrand"
title: "How to install Python 3.6 on Raspbian Linux for Raspberry Pi"
date: "2017-06-30"
image: "raspbianheader.png"
tags: [
  'Raspberry Pi'
]
---

Let's walk through how to get Python 3.6 installed on Raspbian Linux for Raspberry Pi.

I'm assuming you have a recent copy of Raspbian (Jessie) for your Raspberry Pi. If not, either run apt-get to update or download the latest iso.

As I write this, the current distro of Raspbian (Jessie) includes Python 3.4.2. I had a specific requirement for 3.6.0. Here's what I did to get it installed. Note these instructions would also work for 3.6.1.

## Pre-requisites

Open the terminal (command prompt) and run the following commands. These will install the pre-requisites

{{< gist bcnzer 337e3f160924612d0510dcb57bf8b3a0 >}}

## Download Python 3.6

Next step is to [download from the Python](https://www.python.org/downloads/) site. Once you've downloaded it you will need to extract it (hence line 3)

{{< gist bcnzer b3056618a99b30fa064f71a849cd07fa >}}

## Compile Python

Next we need to compile. This step will take a while.

{{< gist bcnzer c269ed2a2f948bcd53ed0c4849f4932b >}}

We're using make altinstall make to avoid having replacing the default python binary file in /usr/bin/python.

## All done!

If you want to confirm Python 3.6 is installed, run **python3.6 -V**. It should respond with **Python 3.6.0**
