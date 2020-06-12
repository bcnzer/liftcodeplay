---
author: "Ben Chartrand"
date: 2019-08-21
title: "DevOps my Terraform’ed Cloudflare"
image: '20190821/patrick-hendry-ju-dqcfyaxe-unsplash.jpg'
tags: [
  'Terraform'
]
---

Let me show you how I was able to create a CI / CD pipeline, using Azure DevOps, for a Terraform implementation of a Cloudflare account.

###### Photo by [Patrick Hendry](https://unsplash.com/@worldsbetweenlines?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/collections/3351204/mountains-and-cliffs/7c72ed4c4c766861c5f3f350646b752b?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Background
When I started my first major project using Terraform project I was did everything locally, as the Terraform documentation suggests. That went well.

Trouble is – it only worked on my system. I really wanted the rest of my team to deploy changes from a centralized location. There were a few key problems:
* Terraform uses a [state file](https://www.terraform.io/docs/state/). How could I store this centrally so anyone could read it and, especially, write to it with changes?
* How can I keep secrets out of git? I don’t want my Cloudflare API key to ever get committed.
* My team needs to review changes (aka `terraform plan`) before they were applied. How could I do that?
* What about PRs? I’d really like to ensure the Terraform is at least valid as part of the PR creation process

# Demo app for this post: wolftracker.nz

For the purpose of this blog post I’ll be using an app I built in 2017 called [Wolf Tracker](https://wolftracker.nz/). You can [read about it here](https://liftcodeplay.com/2017/12/20/wolf-tracker-vue-js-azure-functions-more-for-a-secure-cheap-highly-scalable-spa/).

The setup for this is very simple: three DNS entries and a page rule.

{{< picture "20190821/wolftracker-dns.png" "20190821/wolftracker-dns.png" "DNS entries for wolftracker.nz" >}}

{{< picture "20190821/wolftracker-pagerule.png" "20190821/wolftracker-pagerule.png" "Page rules for wolftracker.nz" >}}

The Terraform file for it is quite simple.

```
variable "CloudflareApiEmail" {}
variable "CloudflareApiKey" {}
 
locals {
  domain-wolftracker = "wolftracker.nz"
  domain-frontend = "wolftrackerfrontend.azurewebsites.net"
  domain-api = "wolftracker.azurewebsites.net"
}
 
# DNS
resource "cloudflare_record" "wolftracker_cname_root" {
  domain  = "${local.domain-wolftracker}"
  name    = "${local.domain-wolftracker}"
  type    = "CNAME"
  ttl     = "1"
  proxied = "true"
  value   = "${local.domain-frontend}"
}
 
resource "cloudflare_record" "wolftracker_cname_www" {
  domain  = "${local.domain-wolftracker}"
  name    = "www.${local.domain-wolftracker}"
  type    = "CNAME"
  ttl     = "1"
  proxied = "true"
  value   = "${local.domain-frontend}"
}
 
resource "cloudflare_record" "wolftracker_cname_api" {
  domain  = "${local.domain-wolftracker}"
  name    = "api.${local.domain-wolftracker}"
  type    = "CNAME"
  ttl     = "1"
  proxied = "true"
  value   = "${local.domain-api}"
}
 
# Page rule
resource "cloudflare_page_rule" "wolftracker_" {
  zone     = "${local.domain-wolftracker}"
  target   = "https://wolftracker.nz/*"
  priority = 1
  actions {
    forwarding_url {
      status_code = 302
      url         = "https://www.wolftracker.nz/"
    }
  }
}

# If you want to run this locally create a secrets.tfvar 
# (see the secrets.example.tfvar file - you can use that as a template) and 
# set a value in each of the three fields. Note that Azure DevOps has a copy
# of the secrets.tfvars file but it's all filled in
 
provider "cloudflare" {
   email = "${var.CloudflareApiEmail}"
   token = "${var.CloudflareApiKey}"
}
 
# *** STATE *** We use Azure Blob storage as our centralized repository
# of all our state for Terraform. If you wanted to try it out yourself
# you could use the config below but any changes you make WOULD AFFECT PRODUCTION.
# terraform {
#  backend "azurerm" {
   #  storage_account_name  = ""
   #  container_name        = "iac-cloudflare-terraform-state"
   #  key                   = "terraform.tfstate"
   #  access_key            = "enter your blob storage key here"
#  }
# }
```

## Azure DevOps

My tool of choice for all things CI / CD is [Azure DevOps](https://azure.microsoft.com/en-in/services/devops/). I use it at work and personally. We’re going to use it for two things:

1. private git repo for hosting our Terraform content
2. [Azure Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/). Microsoft offers 1,800 free build minutes on their hosted agents

# State File

Terraform knows what is in Cloudflare by comparing your .tf files with the content of the state file. Trouble is, my state file is empty!

## Generating a State file

To generate my state file I used the `terraform import` command. Each Terraform resource type has an import command i.e. [here’s one for DNS](https://www.terraform.io/docs/providers/cloudflare/r/record.html) (scroll down to the bottom).

What we’re doing is matching up my Terraform reference with the actual item in Cloudflare (an ID).

To get Cloudflare’s ID for each item, use the [Cloudflare API](https://api.cloudflare.com/) and the `list` command. Below is sample script with all my import commands.

```
# Example import file 
terraform import cloudflare_record.wolftracker_cname_root wolftracker.nz/43c2a5a7c3e4f5ed24d1111111111111
terraform import cloudflare_record.wolftracker_cname_www wolftracker.nz/5e753c7ba40e0c49e842222222222222
terraform import cloudflare_record.wolftracker_cname_api wolftracker.nz/92171df5c2b859228353333333333333
terraform import cloudflare_page_rule.wolftracker_www_page_rule wolftracker.nz/0353eb4c50606480a4d4444444444444
```

## Remote state

We need to host our state file in a central location so anyone or any process can access it.

The easiest option is to use some cloud storage such. Amazon S3 would work but, in my case, I’m using Azure so went with Azure Blob storage. I did the following:
* Found an existing storage account
* Created a container in blob storage called `iac-cloudflare-terraform`
* Kept the default permissions, so it was private. Do not set it public
* Uploaded `terraform.tfstate` from my local PC. Note that this is after I ran the above import commands

## Secrets

To use Cloudflare and Terraform we need a Cloudflare API key and corresponding email address. There’s two parts.

### Secrets – Part 1

1. Created `secrets.tfvars` and entered my credentials. See the example below
2. Setup `.gitignore` to ignore `secrets.tfvars`. That way we can use it locally if want to and not worry about getting it committed. We’ll be uploading it shortly
3. Setup a `secrets.example.tfvars` file, as seen below. It’s a nice-to-have so others know what the expected format is incase they need to create one

```
# enter your details here
CloudflareApiEmail = ""
CloudflareApiKey = ""
```

### Secrets – Part 2

In Azure DevOps they have a feature called [Secure Files](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/secure-files?view=azure-devops). It’s designed for securely storing and using sensitive files such as certificates. It can be found in Pipelines > Library > Secure files.

One there I uploaded my `secrets.tfvars` file.

{{< picture "20190821/2019-08-19_21-11-25.png" "20190821/2019-08-19_21-11-25.png" "Storage of my secrets.tfvars in Secure files" >}}

# Building the CI / CD pipeline
Now the fun begins! We’ll create a build and then create a release process with two steps.

## Download the Terraform Extension
You can execute Terraform commands from a script in Azure DevOps but [this free extension](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform&targetId=1f6f0f73-c512-4198-8bb0-1b8580f447d7) creates a Terraform task that makes everything a bit easier. It’s what I used so go ahead and install it.

## Build – CI
The build step is very simple.
* I chose to set it up so it triggers (runs) on commits going to `master`
* All the files get downloaded from the repo and zipped up
* At work I added a Slack message so I knew the build kicked off

Note that I chose to use the Ubuntu hosted agent (ubuntu-16.04). You can select it by clicking the **Pipeline** header.

{{< picture "20190821/2019-08-21_22-03-24.png" "20190821/2019-08-21_22-03-24.png" "My build process" >}}

## Quick Note – Terraform commands
Incase you’re not aware the key commands are:
* [init](https://www.terraform.io/docs/commands/init.html) – initialize your Terraform project
* [validate](https://www.terraform.io/docs/commands/validate.html) – confirm your files are syntactically correct
* [plan](https://www.terraform.io/docs/commands/plan.html) – tell you what will change
* [apply](https://www.terraform.io/docs/commands/apply.html) – same as plan but once done, asks to apply the change

# Release – CD
The picture below shows what we’re building.

{{< picture "20190821/2019-08-21_20-59-12-1.png" "20190821/2019-08-21_20-59-12-1.png" "The release process we’re building" >}}

The basic idea is:

* The release is triggered whenever a build completes (far left block)
* Run a `terraform plan` and then await approval.
  * The idea is the user should check the log to make sure the plan step makes sense
  * The approval step is enabled by clicking on the icon on the right-hand side of the block and choosing designated approvers
* Once the above is approved, run the next stage which applies our changes

The Terraform plan and apply steps are near identical. I cloned the `terraform plan` group and changed the `plan` step with `apply`.

Below are the steps in my terraform plan stage. Note that the validation step is optional. I added it just incase I uploaded some invalid Terraform code but the plan step would catch it.

{{< picture "20190821/2019-08-21_22-13-32.png" "20190821/2019-08-21_22-13-32.png" "terraform init. Note the configuration of the storage account" >}}

{{< picture "20190821/2019-08-21_22-14-00.png" "20190821/2019-08-21_22-14-00.png" "terraform validate. Note the use of the secrets file" >}}

{{< picture "20190821/2019-08-21_22-14-25.png" "20190821/2019-08-21_22-14-25.png" "terraform plan. Note the use of the secrets file" >}}

## Extra Steps
I would suggest two additional changes:

1. Add Slack notifications, so you know when steps have completed or are awaiting approval
2. You may want to extend this more so that you [capture the output of the plan step and use that in the subsequent apply step](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation). In my case, as changes will happen so infrequently, I don’t care.

# Result!

When I commit to `master` my build runs, collects my files and then triggers the release. I can then review the log and ultimately approve my release – which then applies the changes.

{{< picture "20190821/2019-08-21_22-21-50.png" "20190821/2019-08-21_22-21-50.png" "My release awaiting approval" >}}

{{< picture "20190821/2019-08-21_22-23-15.png" "20190821/2019-08-21_22-23-15.png" "Example of the plan step log for me to review ahead of approving the release" >}}