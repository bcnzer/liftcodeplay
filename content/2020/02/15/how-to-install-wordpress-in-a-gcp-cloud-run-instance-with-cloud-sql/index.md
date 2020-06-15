---
author: "Ben Chartrand"
title: "How to install WordPress in a GCP Cloud Run instance with Cloud SQL"
date: "2020-02-15"
image: "header-1.jpg"
tags: [
  'Firebase'
]
---

The following is how I was able to setup a WordPress instance to run in GCP using [Cloud Run](https://cloud.google.com/run) with a MySQL instance in [Cloud SQL](https://cloud.google.com/sql).

I started using [this](https://medium.com/acadevmy/how-to-install-a-wordpress-site-on-google-cloud-run-828bdc0d0e96) blog post to implement my WordPress site in Cloud Run. While it explained the concepts it was written when Cloud Run was in beta, it didn't explain key details such as how exactly to include plugins & themes and just didn't work if you updated WordPress. This blog post addresses all those points.

## Background Info

### Cloud Run

[Cloud Run](https://cloud.google.com/run) is a serverless compute platform for containers. I'm using their managed version which means they take care of all the underlying details.

The appeal of Cloud Run is the overall cost. It should be very cheap, especially as my initial traffic will be very low (just me, chipping away at it). Also, being a serverless, managed offering, it will scale if/when it needs to.

A key thing to understand is that it's designed to run **stateless** containers. You cannot expect anything in memory or the hard disk to persist. **If you want to upgrade your WordPress, add/update themes or plugins you must update the Docker image used by Cloud Run.**

I saw this first-hand as the plugins and themes I installed would magically disappear after even a few minutes of use. I'll show you how I overcame this.

### Cloud SQL

I setup a MySQL database in [Cloud SQL](https://cloud.google.com/sql). It's a managed service (PaaS) so I don't have to worry about any of the underlying details.

I'll be create the smallest instance possible - the f1 micro. You can scale it up later or change it right away in the scripts.

Given the database is not on the same box as WordPress and we don't want to use an IP address, we'll use something called Cloud SQL Proxy to connect.

Part of the charge for Cloud SQL is the time it is running so, if you're not using it or are still building it, turning it off is a good idea.

## gcloud CLI

I'm going to create all the GCP resources from the command line, using the [gcloud CLI](https://cloud.google.com/sdk/gcloud).

While you can do most of these steps in the UI you will need the CLI installed in order to upload your docker container to the container registry.

## Prerequisites

You'll need the following:

- GCP account
    - If you [create one](https://cloud.google.com/gcp/) you'll get $300 in free credits for 12 months
    - Project created in the portal
    - Cloud Run API must be enabled
- Your favourite text editor and command line
- gcloud CLI and SDK installed. Follow the instructions [here](https://cloud.google.com/sdk/gcloud#the_gcloud_cli_and_cloud_sdk)
- Optional: install Docker on your system

## Source

Download / clone the following repo.

[https://github.com/bcnzer/cloudrun-cloudsql-wordpress](https://github.com/bcnzer/cloudrun-cloudsql-wordpress).

You will need to do some configuration, so let's walk through that now and explain what we're up to.

## Setup

### Step 1 - Add an IAM role

In the GCP portal navigate to IAM. You need to find a service account that ends with **@serverless-robot-prod.iam.gserviceaccount.com** so enter that in the filter.

Once you've found it add the **Cloud SQL Client** role.

[![](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_14-17-02.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_14-17-02.png)

Cloud SQL Client role added in IAM

### Step 2 - Enable Cloud SQL Admin

Enable Cloud SQL Admin APIs visiting **https://console.developers.google.com/apis/library/sqladmin.googleapis.com?project=\[PROJECT\_ID\]**

Make sure to replace \[PROJECT\_ID\] with your id. One there click **Enable**.

### Step 3 - Set the Unique Keys and Salts

With the **wordpress/wp-config.php** file there's a section **Authentication Unique Keys and Salts**. Looks like this.

[![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-121959.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-121959.png)

Config file with the keys/salts entered

You need to change this. Follow [the link](https://api.wordpress.org/secret-key/1.1/salt/ ) you can see in the comments. It will generate some unique values for you. Copy and paste those in. The end result should look something like this.

[![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-122259.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-122259.png)

Config file WITH keys/salts entered

### Step 4 - Set the Variables in the script

There's a [setup.sh](https://github.com/bcnzer/cloudrun-cloudsql-wordpress/blob/master/setup.sh) file with a bunch of variables at the top. Here's the same file as a gist

https://gist.github.com/bcnzer/aa93c771b7c016cc61d9695b7ec76d63

You need to enter in value wherever you see **<TODO>**. The comments explains each but let's go through the ones we need to setup:

- **PROJECT\_NAME** is the project name from the GCP portal. Not to be confused with the descriptive name. You can see it in the URL of the portal.
    - Example: if you see the following URL  
        _console.cloud.google.com/run?project=**my-cr-project**&folder=&organizationId=_  
        then the project name is **my-cr-project**
- **ROOT\_PSWD** is the password for the root database. Enter a strong, unique password
- **USER\_PSWD** is the password for the `wordpress` user, against the actual database you setup. Enter a strong, unique password

I would suggest you review the region and zone. Be aware that, as of this writing, Cloud Run is not available in all regions though it is gradually rolling out. Checkout [this site](https://cloud.google.com/run/docs/locations) for a list of supported regions.

Note that you can also change the size of the MySQL DB instance on line 4. The complete list of tiers is available [here](https://cloud.google.com/sql/pricing#2nd-gen-pricing).

#### Script file explained

- Lines **1-11** are all the variables we need to set / review. I created variables as we often need to reuse them and makes it easy, as you only need to enter/change data here
- Lines **13-16** are ensuring the project name, region and zone are set for all the following commands.
- Lines **18-24** first creates the MySQL instance, database and sets users/passwords for both. Note that this step takes a while to execute
- Line **31** create the docker image and uploads it to the container registry in GCP
- Line **34-37** does a bunch of things:
    - creates the cloud run instance with your image
    - points to the Cloud SQL instance
    - sets a bunch of environment variables WordPress needs
    - specifies you're using the _managed_ version of Cloud Run (remove **\--platform=managed** and, instead, it'll ask)

### Step 5 - Plugins and Themes

#### Background

Cloud Run is stateless. You can lose anything on the hard disk or in memory at any time.

This is a problem for plugins and themes. It means you can't just install them. They need to be pre-installed via the Docker image. There's two ways you can do it:

1. Incorporate the WordPress CLI into your docker image
2. Copy the files as part of a docker step (what I did) and, later, activate the individual plugins

In the repo there is a **plugins** and **themes** folder. The DockerFile (lines 8 and 11) copies them to  
**/usr/src/wordpress/wp-content/themes/** and  
**/usr/src/wordpress/wp-content/plugins/**

Upon initialization of WordPress the contents are copied to  
**/var/www/html/wp-content/themes/** and  
**/var/www/html/wp-content/plugins/**

#### Adding your own Plugins and Themes

To add your own simply download the theme/plugin and put them in the respective folder.

I've gone ahead and added a theme called _mesmerize_ and some plugins _wp-stateless_, _static-html-output-plugin_ and _file-manager-advanced_. Feel free to delete these. I added them as examples.

Here's how I added wp-stateless, which we will need shortly:

- Found [the URL](https://wordpress.org/plugins/wp-stateless/) for the wp-stateless extension on WordPress
- Clicked **Download**
- Unzipped the contents to my **plugins** directory

Note that you could change the Dockerfile to download the themes / plugins fresh each time (similar to the Cloud SQL Proxy) but you risk getting an version that could have compatibility issues. Up to you!

### Step 6 - Run the script

Start the **setup.sh** script and wait. The setup of the MySQL instance takes time.

Right near the end you will be asked **Allow unauthenticated invocations to \[wordpress\] (y/N)**. Make sure you enter **y**.

This is important. Without this people will not be able to browser to your website.

### Step 7 - Login to WordPress

Right at the end the command you will see a URL. Copy and paste that URL into your browser and you should see the WordPress setup screen!

[![](https://liftcodeplay.files.wordpress.com/2020/02/cmd.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/cmd.png)

Setup complete! The Cloud Run instance is live for you

You can also get the URL by looking at the Cloud Run instance in the GCP portal.

![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-121960.png?w=828)

The Cloud Run instance is live!

[![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-122259-1.png?w=723)](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-122259-1.png)

The URL is visible once you click on the specific instance

### Step 8 - Activate Plugins

We installed the plugins but they're not active. The active state is tored in the database so we only need to do this once.

- Go into the admin portal
- **Plugins** \> **Installed Plugins**
- Find the desired plugin and click **Activate**

While you're here active the **WP-Stateless** plugin.

[![](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_13-59-55.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_13-59-55.png)

The WP-Stateless Plugin

### Step 9 - WP-Stateless

Once again, Cloud Run is stateless. Any media you upload will be lost. To solve this we're using a plugin called WP-Stateless that allows us to store everything in a GCP Storage bucket.

Once activated you can follow the prompts to quickly setup. It will get you to login with your account, create a storage account in GCP and more. [Otherwise you can set it up manually](https://geekflare.com/wordpress-media-google-cloud-storage/).

[![](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_14-05-47.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_14-05-47.png)

WP-Stateless setup screen

### Step 10 - Set a domain

As it's a public site we don't want users to go to the URL provided by Cloud Run. We want it to go to a domain we own.

- In the Cloud Run page click **Manage Custom Domains**
- Click **Add Mapping**
- Follow the prompts. You'll be asked to enter a bunch of DNS entries wherever you host your domain
- You may need to wait for the DNS to take affect

Note that you need a second domain for the www version of your site as it's technically a subdomain.

The end result is something like this.

[![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-142259.png?w=743)](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-142259.png)

Domain entries successfully setup. Note that the URL doesn't actually exist

### Step 11 - Set the WordPress & Site Address

Once you've setup a domain make sure to set the WordPress Address (URL) and Site Address (URL) in **Settings** \> **General**.

If you don't do this any link you or your users click will resolve to your Cloud Run URL.

[![](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_21-00-14-1.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/2020-02-15_21-00-14-1.png)

Make sure to enter your domain here

## DONE!

Congrats - at this point you have a working WordPress site!

I've clearly got a lot more work, below, to do to get it setup with all my content but it's a good start.

![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-15-142459.png?w=947)

Hooray - my site works!

## Appendum 1: Pushing an update

So let's say you want to push an update to your live site. For example: perhaps you added some plugins. How do you do it?

- Open the script
- Comment out the database setup (lines 19-24)
- I'd suggest you change line 31 so it says **v2**. That way you keep your current Docker image as v1. If you don't do this, your previous v1 image loses the v1 moniker but otherwise it sticks around. You don't lose it.
- Run the script!

## Appendum 2: the Dockerfile

I chose to focus this blog post on how to get up and running. [The blog post I mentioned at the start of the project](https://medium.com/acadevmy/how-to-install-a-wordpress-site-on-google-cloud-run-828bdc0d0e96) explains much of the detail and context around the Dockerfile, Apache config and more.

There are a few key differences and details I wanted to mention:

- I'm using the latest version, as of this writing
- I added the COPY steps for themes and plugins
- Changed the download of Cloud SQL Proxy to use curl instead of wget. The install of apt-get resulted in errors with the latest WordPress.
